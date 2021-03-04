---
title: Creare un'origine dati per le mappe Android | Mappe Microsoft Azure
description: "Informazioni su come creare un'origine dati per una mappa. Informazioni sulle origini dati usate da Azure Maps Android SDK: origini GeoJSON e riquadri vettoriali."
author: rbrundritt
ms.author: richbrun
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fc68dc25aad3671a55e5c11cbee094b4027e7070
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047673"
---
# <a name="create-a-data-source-android-sdk"></a>Creare un'origine dati (Android SDK)

Azure Maps Android SDK archivia i dati nelle origini dati. L'utilizzo di origini dati ottimizza le operazioni di dati per l'esecuzione di query e il rendering. Attualmente esistono due tipi di origini dati:

- **Origine GeoJSON**: gestisce i dati di posizione non elaborati in formato GeoJSON localmente. Ideale per set di dati di piccole e medie dimensioni (a partire da centinaia di migliaia di forme).
- **Origine del riquadro vettoriale**: carica i dati formattati come riquadri vettoriali per la vista mappa corrente, in base al sistema di affiancamento mappe. Ideale per set di dati di grandi dimensioni (milioni o miliardi di forme).

## <a name="geojson-data-source"></a>Origine dati GeoJSON

Azure Maps usa GeoJSON come uno dei modelli di dati primari. GeoJSON è un modo standard Open Geospatial per la rappresentazione di dati geospaziali in formato JSON. Le classi GeoJSON disponibili nell'Android SDK mappe di Azure semplificano la creazione e la serializzazione dei dati GeoJSON. Caricare e archiviare i dati GeoJSON nella `DataSource` classe ed eseguirne il rendering usando i livelli. Il codice seguente mostra come è possibile creare oggetti GeoJSON in mappe di Azure.

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

In alternativa, le proprietà possono essere caricate in un JsonObject prima quindi passate alla funzionalità durante la creazione, come illustrato di seguito.

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

Una volta creata una funzionalità GeoJSON, è possibile aggiungere un'origine dati alla mappa tramite la `sources` proprietà della mappa. Nel codice seguente viene illustrato come creare un oggetto `DataSource` , aggiungerlo alla mappa e aggiungere una funzionalità all'origine dati.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

Il codice seguente illustra diversi modi per creare una funzionalità GeoJSON, Featurecollection e geometrie.

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

### <a name="serialize-and-deserialize-geojson"></a>Serializzare e deserializzare GeoJSON

La raccolta di funzionalità, la funzionalità e le classi Geometry hanno tutti i `fromJson()` `toJson()` metodi e statici, che contribuiscono alla serializzazione. La stringa JSON valida formattata passata tramite il `fromJson()` metodo creerà l'oggetto Geometry. Questo `fromJson()` Metodo significa anche che è possibile usare Gson o altre strategie di serializzazione/deserializzazione. Il codice seguente illustra come eseguire una funzionalità GeoJSON file e deserializzarla nella classe feature, quindi serializzarla di nuovo in una stringa GeoJSON.

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importare i dati GeoJSON dal Web o dalla cartella assets

La maggior parte dei file GeoJSON contiene un elemento Featurecollection. Leggere i file GeoJSON come stringhe e usare il `FeatureCollection.fromJson` metodo per deserializzarlo.

Il codice seguente è una classe riutilizzabile per l'importazione di dati dalla cartella risorse Web o locali come stringa e la restituzione al thread dell'interfaccia utente tramite una funzione di callback.

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

Il codice seguente illustra come usare questa utilità per importare i dati GeoJSON come stringa e restituirli al thread dell'interfaccia utente tramite un callback. Nel callback i dati di stringa possono essere serializzati in una raccolta di funzionalità GeoJSON e aggiunti all'origine dati. Facoltativamente, aggiornare la fotocamera Maps per concentrarsi sui dati.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

## <a name="vector-tile-source"></a>Origine riquadro vettoriale

Un'origine del riquadro vettoriale descrive come accedere a un livello tessera vettoriale. Usare la `VectorTileSource` classe per creare un'istanza di un'origine del riquadro vettoriale. I livelli dei riquadri vettoriali sono simili ai livelli affiancati, ma non sono uguali. Un livello sezione è un'immagine raster. I livelli del riquadro vettoriale sono file compressi in formato **PBF** . Questo file compresso contiene i dati della mappa vettoriale e uno o più livelli. È possibile eseguire il rendering e lo stile del file nel client, in base allo stile di ogni livello. I dati in un riquadro vettoriale contengono funzionalità geografiche sotto forma di punti, linee e poligoni. Esistono diversi vantaggi derivanti dall'uso di livelli di tessera vettoriale invece dei livelli di riquadri raster:

- Una dimensione del file di un riquadro vettoriale è in genere molto più piccola rispetto a un riquadro raster equivalente. Di conseguenza, viene utilizzata una minore larghezza di banda. Ovvero una latenza più bassa, una mappa più veloce e un'esperienza utente migliore.
- Poiché viene eseguito il rendering dei riquadri vettoriali nel client, si adattano alla risoluzione del dispositivo in cui vengono visualizzati. Di conseguenza, le mappe sottoposte a rendering vengono visualizzate in modo più ben definito, con etichette Clear Crystal.
- Per modificare lo stile dei dati nelle mappe vettoriali, non è necessario scaricare di nuovo i dati, perché il nuovo stile può essere applicato nel client. Al contrario, la modifica dello stile di un livello sezione raster richiede in genere il caricamento di riquadri dal server, quindi l'applicazione del nuovo stile.
- Poiché i dati vengono recapitati in forma vettoriale, per preparare i dati è necessaria una minore elaborazione sul lato server. Di conseguenza, i dati più recenti possono essere resi più veloci.

Mappe di Azure rispetta la [specifica del riquadro vettoriale MapBox](https://github.com/mapbox/vector-tile-spec), uno standard aperto. Azure Maps fornisce i servizi dei riquadri vettoriali seguenti come parte della piattaforma:

- Dettagli del [](/rest/api/maps/renderv2/getmaptilepreview)  |  [formato dei dati](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) della documentazione sui riquadri stradali
- [](/rest/api/maps/traffic/gettrafficincidenttile)  |  [Dettagli del formato dati](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) della documentazione sugli eventi imprevisti del traffico
- Dettagli del [](/rest/api/maps/traffic/gettrafficflowtile)  |  [formato dati](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) della documentazione del flusso di traffico
- Azure Maps Creator consente inoltre la creazione e l'accesso ai riquadri vettoriali personalizzati tramite il [rendering del riquadro Get V2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Quando si usano i riquadri di immagini Vector o raster del servizio di rendering di Azure Maps con SDK Web, è possibile sostituire `atlas.microsoft.com` con il segnaposto `azmapsdomain.invalid` . Questo segnaposto verrà sostituito con lo stesso dominio utilizzato dalla mappa e aggiungerà automaticamente anche i dettagli di autenticazione. Questo semplifica notevolmente l'autenticazione con il servizio di rendering quando si usa l'autenticazione Azure Active Directory.

Per visualizzare i dati da un'origine del riquadro vettoriale sulla mappa, connettere l'origine a uno dei livelli di rendering dei dati. Tutti i livelli che usano un'origine vettore devono specificare un `sourceLayer` valore nelle opzioni. Il codice seguente carica il servizio Tile del vettore del flusso di traffico di Azure Maps come origine del riquadro vettoriale, quindi lo Visualizza su una mappa usando un livello linea. Questa origine del riquadro vettoriale dispone di un singolo set di dati nel livello di origine denominato "flusso del traffico". Nei dati della riga di questo set di dati è presente una proprietà denominata `traffic_level` utilizzata in questo codice per selezionare il colore e ridimensionare le linee.

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

![Mappa con linee stradali codificate con colori che mostrano i livelli di flusso del traffico](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Connessione di un'origine dati a un livello

Viene eseguito il rendering dei dati sulla mappa utilizzando i livelli di rendering. È possibile fare riferimento a una singola origine dati da uno o più livelli di rendering. Per i seguenti livelli di rendering è necessaria un'origine dati:

- [Bubble layer](map-add-bubble-layer-android.md) : esegue il rendering dei dati punto come cerchi ridimensionati sulla mappa.
- [Livello simboli](how-to-add-symbol-to-android-map.md) : esegue il rendering dei dati del punto come icone o testo.
- [Livello mappa termica](map-add-heat-map-layer-android.md) : esegue il rendering dei dati punto come mappa termica della densità.
- [Livello linea](android-map-add-line-layer.md) : esegue il rendering di una linea e o esegue il rendering della struttura dei poligoni.
- [Livello poligono](how-to-add-shapes-to-android-map.md) : riempie l'area di un poligono con un colore a tinta unita o un modello di immagine.

Il codice seguente illustra come creare un'origine dati, aggiungerla alla mappa e connetterla a un livello Bubble. Quindi, importare i dati del punto GeoJSON da una posizione remota nell'origine dati.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

Sono disponibili livelli di rendering aggiuntivi che non si connettono a queste origini dati, ma caricano direttamente i dati per il rendering.

- [Livello sezione](how-to-add-tile-layer-android-map.md) : sovrappone un livello sezione raster sopra la mappa.

## <a name="one-data-source-with-multiple-layers"></a>Un'origine dati con più livelli

È possibile connettere più livelli a un'unica origine dati. Questa opzione è utile in molti scenari diversi. Si consideri, ad esempio, lo scenario in cui un utente disegna un poligono. È necessario eseguire il rendering e riempire l'area del poligono quando l'utente aggiunge punti alla mappa. L'aggiunta di una linea con stile per delineare il poligono consente di visualizzare più facilmente i bordi del poligono, quando viene disegnato dall'utente. Per modificare agevolmente una singola posizione nel poligono, è possibile aggiungere un handle, ad esempio un PIN o un marcatore, al di sopra di ogni posizione.

![Mappa che Mostra più livelli di rendering dei dati da una singola origine dati](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

Nella maggior parte delle piattaforme di mapping sono necessari un oggetto poligono, un oggetto linea e un PIN per ogni posizione nel poligono. Quando si modifica il poligono, è necessario aggiornare manualmente la linea e i pin, che possono diventare rapidamente complessi.

Con le mappe di Azure, è sufficiente un singolo poligono in un'origine dati, come illustrato nel codice riportato di seguito.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

> [!TIP]
> Quando si aggiungono livelli alla mappa utilizzando il `map.layers.add` metodo, l'ID o l'istanza di un livello esistente può essere passata come secondo parametro. Questo indicherebbe a map di inserire il nuovo livello aggiunto al di sotto del livello esistente. In aggiunta per passare un ID livello questo metodo supporta anche i valori seguenti.
>
> - `"labels"` -Inserisce il nuovo livello sotto i livelli dell'etichetta della mappa.
> - `"transit"` -Inserisce il nuovo livello sotto la strada e i livelli di transito della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Esempi di codice dell'SDK Web](/samples/browse/?products=azure-maps)