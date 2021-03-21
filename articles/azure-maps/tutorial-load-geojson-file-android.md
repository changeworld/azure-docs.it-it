---
title: 'Esercitazione: Caricare i dati GeoJSON in Android SDK per Mappe di Azure | Mappe di Microsoft Azure'
description: Esercitazione su come caricare il file di dati GeoJSON in Android SDK per Mappe di Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 8300a7c120ce816c8068a88fa69f4f978fa664ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034507"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Esercitazione: Caricare dati GeoJSON in Android SDK per Mappe di Azure

Questa esercitazione illustra il processo di importazione di un file GeoJSON dei dati di posizione in Android SDK per Mappe di Azure. In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Aggiungere Mappe di Azure a un'applicazione Android.
> * Creare un'origine dati e caricarla in un file GeoJSON da un file locale o dal Web.
> * Visualizzare i dati sulla mappa.

## <a name="prerequisites"></a>Prerequisiti

1. Completare l'articolo [Avvio rapido: Creare un'app Android](quick-android-map.md). Questa esercitazione estenderà il codice usato in tale argomento di avvio rapido.
2. Scaricare il file GeoJSON dei [punti di interesse di esempio](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json).

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importare i dati GeoJSON dal Web o dalla cartella assets

La maggior parte dei file GeoJSON esegue il wrapping di tutti i dati in un oggetto `FeatureCollection`. Tenendo presente questo aspetto, se i file GeoJSON vengono caricati nell'applicazione sotto forma di stringa, possono essere passati al metodo statico della raccolta di funzionalità `fromJson` , che deserializzano la stringa in un oggetto GeoJSON `FeatureCollection` che può essere aggiunto alla mappa.

I passaggi seguenti illustrano come importare un file GeoJSON nell'applicazione e deserializzarlo come oggetto GeoJSON `FeatureCollection`.

1. Completare l'articolo [Avvio rapido: Creare un'app Android](quick-android-map.md) poiché i passaggi seguenti si basano su questa applicazione.
2. Nel pannello progetto di Android Studio fare clic con il pulsante destro del mouse sulla cartella dell' **app** e passare a `New > Folder > Assets Folder` .
3. Trascinare e rilasciare il file GeoJSON dei [punti di interesse di esempio ](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) nella cartella assets.

::: zone pivot="programming-language-java-android"

4. Creare un nuovo file denominato **Utils.java** e aggiungere il codice seguente a tale file. Questo codice fornisce un metodo statico chiamato `importData` che importa in modo asincrono un file dalla `assets` cartella dell'applicazione o dal Web usando un URL come stringa e lo restituisce al thread UI usando un metodo di callback semplice.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. Passare al file **MainActivity.java** e aggiungere il codice seguente all'interno del callback per l'evento `mapControl.onReady`, che si trova all'interno del metodo `onCreate`. Questo codice usa l'utilità di importazione per leggere il file **SamplePoiDataSet.json** come stringa, quindi lo deserializza come raccolta di funzionalità usando il metodo statico `fromJson` della classe `FeatureCollection`. Questo codice calcola anche l'area del rettangolo delimitatore per tutti i dati nella raccolta di funzionalità e la usa per impostare la fotocamera della mappa per concentrarsi sui dati.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
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

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Usando il codice per caricare i dati GeoJSON in un'origine dati, è ora necessario specificare la modalità di visualizzazione dei dati sulla mappa. Sono disponibili diversi livelli di rendering per i dati dei punti. Il [livello bolla](map-add-bubble-layer-android.md), il [livello simbolo](how-to-add-symbol-to-android-map.md) e il [livello mappa termica](map-add-heat-map-layer-android.md) sono i livelli usati più di frequente. Aggiungere il codice seguente per eseguire il rendering dei dati in un livello bolla nel callback per l'evento `mapControl.onReady` dopo il codice per l'importazione dei dati.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Creare un nuovo file denominato **utils. kt** e aggiungere il codice seguente al file. Questo codice fornisce un metodo statico chiamato `importData` che importa in modo asincrono un file dalla `assets` cartella dell'applicazione o dal Web usando un URL come stringa e lo restituisce al thread UI usando un metodo di callback semplice.

    ```kotlin
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;

    import android.content.Context
    import android.os.Handler
    import android.os.Looper
    import android.webkit.URLUtil
    import java.net.URL
    import java.util.concurrent.ExecutorService
    import java.util.concurrent.Executors
    
    class Utils {
        companion object {
    
            /**
             * Imports data from a web url or asset file name and returns it to a callback.
             * @param urlOrFileName A web url or asset file name that points to data to load.
             * @param context The context of the app.
             * @param callback The callback function to return the data to.
             */
            fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
                importData(urlOrFileName, context, callback, null)
            }
    
            /**
             * Imports data from a web url or asset file name and returns it to a callback.
             * @param urlOrFileName A web url or asset file name that points to data to load.
             * @param context The context of the app.
             * @param callback The callback function to return the data to.
             * @param error A callback function to return errors to.
             */
            public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
                if (urlOrFileName != null && callback != null) {
                    val executor: ExecutorService = Executors.newSingleThreadExecutor()
                    val handler = Handler(Looper.getMainLooper())
                    executor.execute {
                        var data: String? = null
                        
                        try {
                            data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                                URL(urlOrFileName).readText()
                            } else { //Assume file is in assets folder.
                                context.assets.open(urlOrFileName).bufferedReader().use{
                                    it.readText()
                                }
                            }
    
                            handler.post {
                                //Ensure the resulting data string is not null or empty.
                                if (data != null && !data.isEmpty()) {
                                    callback(data)
                                } else {
                                    error!!("No data imported.")
                                }
                            }
                        } catch (e: Exception) {
                            error!!(e.message)
                        }
                    }
                }
            }
        }
    }
    ```

5. Passare al file **MainActivity. kt** e aggiungere il codice seguente all'interno del callback per l' `mapControl.onReady` evento, che si trova all'interno del `onCreate` metodo. Questo codice usa l'utilità di importazione per leggere il file **SamplePoiDataSet.json** come stringa, quindi lo deserializza come raccolta di funzionalità usando il metodo statico `fromJson` della classe `FeatureCollection`. Questo codice calcola anche l'area del rettangolo delimitatore per tutti i dati nella raccolta di funzionalità e la usa per impostare la fotocamera della mappa per concentrarsi sui dati.

    ```kotlin
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json", this) { 
        result: String? ->
            //Parse the data as a GeoJSON Feature Collection.
             val fc = FeatureCollection.fromJson(result!!)
    
            //Add the feature collection to the data source.
            source.add(fc)
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            val bbox = MapMath.fromData(fc);

            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            )
        }
    ```

6. Usando il codice per caricare i dati GeoJSON in un'origine dati, è ora necessario specificare la modalità di visualizzazione dei dati sulla mappa. Sono disponibili diversi livelli di rendering per i dati dei punti. Il [livello bolla](map-add-bubble-layer-android.md), il [livello simbolo](how-to-add-symbol-to-android-map.md) e il [livello mappa termica](map-add-heat-map-layer-android.md) sono i livelli usati più di frequente. Aggiungere il codice seguente per eseguire il rendering dei dati in un livello bolla nel callback per l'evento `mapControl.onReady` dopo il codice per l'importazione dei dati.

    ```kotlin
    //Create a layer and add it to the map.
    val layer = new BubbleLayer(source)
    map.layers.add(layer)
    ```

::: zone-end

7. Eseguire l'applicazione. Verrà visualizzata una mappa focalizzata sugli Stati Uniti, con cerchi sovrapposti per ogni posizione nel file GeoJSON.

    ![Mappa degli Stati Uniti con la visualizzazione dei dati di un file GeoJSON](media/tutorial-load-geojson-file-android/android-import-geojson.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse dell'esercitazione, seguire questa procedura:

1. Chiudere Android Studio ed eliminare l'applicazione creata.
2. Se l'applicazione è stata testata in un dispositivo esterno, disinstallarla da tale dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice e un'esperienza di codifica interattiva:

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Visualizzare informazioni sulle funzionalità](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)
