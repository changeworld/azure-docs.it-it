---
title: Richiedere dati di elevazione usando il servizio di elevazione delle mappe di Azure (anteprima)
description: Informazioni su come richiedere dati di elevazione usando il servizio di elevazione delle mappe di Azure (anteprima).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d14eda84144105bf2e04f1238284bc58a91c4c03
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684056"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Richiedere dati di elevazione usando il servizio di elevazione delle mappe di Azure (anteprima)

> [!IMPORTANT]
> Il servizio di elevazione delle mappe di Azure è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il servizio di [elevazione](/rest/api/maps/elevation) delle mappe di Azure fornisce API per eseguire query sui dati di elevazione ovunque sulla superficie terrestre. È possibile richiedere dati di elevazione campionati lungo percorsi, all'interno di un rettangolo di delimitazione definito o coordinate specifiche. È anche possibile usare l' [API del riquadro di rendering V2-Get mappa](/rest/api/maps/renderv2) per recuperare i dati di elevazione in formato affiancato. I riquadri vengono recapitati in formato raster GeoTIFF. Questo articolo illustra come usare il servizio di elevazione delle mappe di Azure e l'API del riquadro Ottieni mappa per richiedere dati di elevazione. I dati di elevazione possono essere richiesti nei formati GeoJSON e GeoTIFF.

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Azure Maps nel piano tariffario S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione

Per altre informazioni sull'autenticazione in mappe di Azure, [gestire l'autenticazione in mappe di Azure](how-to-manage-authentication.md).

Questo articolo usa l'applicazione [post](https://www.postman.com/) , ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Richiedere dati di elevazione in formato raster

Per richiedere dati di elevazione in formato di riquadro raster, usare l' [API del riquadro render V2-Get Map](/rest/api/maps/renderv2). Se è possibile trovare il riquadro, l'API restituisce il riquadro come GeoTIFF. In caso contrario, l'API restituisce 0. Tutti i riquadri di tipo DEM raster usano la modalità Earth Geoid (livello Sea). In questo esempio verranno richiesti i dati di elevazione per mt. Everest.

>[!TIP]
>Per recuperare un riquadro in un'area specifica della mappa del mondo, è necessario trovare il riquadro corretto al livello di zoom appropriato. Si noti anche che WorldDEM copre l'intera continentale globale, ma non copre gli oceani.  Per altre informazioni, vedere [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

1. Aprire l'app Postman. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta).  Assegnare un nome alla raccolta e selezionare **Create** (Crea).

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente per richiedere il riquadro raster. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Fare clic sul pulsante **Send** (Invia). Si dovrebbe ricevere il riquadro raster contenente i dati di elevazione in formato GeoTIFF. Ogni pixel nei dati non elaborati del riquadro raster è di tipo `float` . Il valore di ogni pixel rappresenta l'altezza di elevazione in metri.

## <a name="request-elevation-data-in-geojson-format"></a>Richiedere dati di elevazione in formato GeoJSON

Usare le API del servizio di elevazione (anteprima) per richiedere dati di elevazione in formato GeoJSON. In questa sezione viene illustrata una delle tre API seguenti:

* [Ottenere i dati per i punti](/rest/api/maps/elevation/getdataforpoints)
* [Inserire i dati per i punti](/rest/api/maps/elevation/postdataforpoints)
* [Recuperare i dati per la polilinea](/rest/api/maps/elevation/getdataforpolyline)
* [Dati post per polilinea](/rest/api/maps/elevation/postdataforpolyline)
* [Recuperare i dati per il rettangolo di delimitazione](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Quando non è possibile restituire dati, tutte le API restituiscono `0` .

### <a name="request-elevation-data-for-points"></a>Richiedere dati di elevazione dei punti

In questo esempio si userà l' [API Get Data for Points](/rest/api/maps/elevation/getdataforpoints) per richiedere dati di elevazione su Mt. Everest e Chamlang Mountains. Si userà quindi l' [API post data for Points](/rest/api/maps/elevation/postdataforpoints) per richiedere dati di elevazione usando gli stessi due punti. La latitudine e la longitudine nell'URL devono trovarsi in un livello decimale WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >A causa del limite di lunghezza del carattere URL 2048, non è possibile passare più di 100 coordinate come stringa delimitata da pipeline in una richiesta URL GET. Se si intende passare più di 100 coordinate come stringa delimitata da pipeline, usare i dati POST per i punti.

1. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Fare clic sul pulsante **Send** (Invia).  Si riceverà la risposta JSON seguente:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. A questo punto, chiameremo l' [API post data for Points](/rest/api/maps/elevation/postdataforpoints) per ottenere i dati di elevazione per gli stessi due punti. Selezionare il metodo HTTP **post** nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Negli **Headers** (Intestazioni) della richiesta **POST**, impostare `Content-Type` su `application/json`. Nel **corpo** fornire le informazioni sul punto di coordinate sotto. Al termine, fare clic su **Send**(Invia).

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Esempi di dati di elevazione delle richieste lungo una polilinea

In questo esempio, si userà il [Get Data for polilinea](/rest/api/maps/elevation/getdataforpolyline) per richiedere cinque esempi equidistanti di dati di elevazione lungo una linea retta tra le coordinate in Mt. Everest e Chamlang Mountains. Entrambe le coordinate devono essere definite in formato Long/Lat. Se non si specifica un valore per il `samples` parametro, il numero di campioni predefinito è 10. Il numero massimo di campioni è 2.000.

Si utilizzerà quindi il Get data per polilinea per richiedere tre esempi equidistanti di dati di elevazione dei privilegi lungo un percorso. Si definirà la posizione esatta per gli esempi passando tre coppie di coordinate Long/Lat.

Infine, si utilizzeranno i [dati post per l'API polilinea](/rest/api/maps/elevation/postdataforpolyline) per richiedere dati di elevazione delle stesse tre esempi equidistanti.

La latitudine e la longitudine nell'URL devono trovarsi in un livello decimale WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >A causa del limite di lunghezza del carattere URL 2048, non è possibile passare più di 100 coordinate come stringa delimitata da pipeline in una richiesta URL GET. Se si intende passare più di 100 coordinate come stringa delimitata da pipeline, usare i dati POST per i punti.

1. Selezionare **Nuovo**. Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Fare clic sul pulsante **Send** (Invia).  Si riceverà la risposta JSON seguente:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Verranno ora richiesti tre esempi di dati di elevazione dei privilegi lungo un percorso tra le coordinate di Mount Everest, Chamlang e Jannes Mountains. Nella sezione **params** copiare la seguente matrice di coordinate per il valore della chiave di `lines` query.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Modificare il `samples` valore della chiave di query in `3` .  L'immagine seguente mostra i nuovi valori.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Recuperare tre campioni di dati di elevazione.":::

6. Fare clic sul pulsante di **invio** blu. Si riceverà la risposta JSON seguente:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. A questo punto, chiameremo i [dati post per l'API polilinea](/rest/api/maps/elevation/postdataforpolyline) per ottenere i dati di elevazione per gli stessi tre punti. Selezionare il metodo HTTP **post** nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. Negli **Headers** (Intestazioni) della richiesta **POST**, impostare `Content-Type` su `application/json`. Nel **corpo** fornire le informazioni sul punto di coordinate sotto. Al termine, fare clic su **Send**(Invia).

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Richiedi dati di elevazione per riquadro

A questo punto si userà il [riquadro recuperare i dati per il delimitatore](/rest/api/maps/elevation/getdataforboundingbox) per richiedere dati di elevazione vicino a mt. Rainier, WA. I dati di elevazione saranno restituiti in posizioni equidistanti all'interno di un rettangolo di delimitazione. L'area di delimitazione definita da (2) set di coordinate Lat/Long (Latitudine Sud, longitudine ovest | latitudine nord, Longitudine est) è divisa in righe e colonne. Bordi dell'account del rettangolo di delimitazione per due (2) delle righe e due (2) delle colonne. Le elevazioni vengono restituite per i vertici della griglia creati nelle intersezioni di riga e colonna. In una singola richiesta possono essere restituite fino a 2000 elevazioni.

In questo esempio si specificheranno Rows = 3 e Columns = 6. nella risposta vengono restituiti 18 valori di elevazione. Nel diagramma seguente i valori di elevazione sono ordinati a partire dall'angolo sud-ovest, quindi continuano da ovest a est e da sud verso nord.  I punti di elevazione sono numerati nell'ordine in cui sono stati restituiti.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Coordinate del rettangolo di delimitazione in corrispondenza degli angoli di e SE.":::

1. Selezionare **Nuovo**. Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Fare clic sul pulsante di **invio** blu. 18 esempi di dati di elevazione, uno per ogni vertice della griglia, vengono restituiti nella risposta.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Esempi: usare le API del servizio di elevazione (anteprima) nel controllo Maps di Azure

### <a name="get-elevation-data-by-coordinate-position"></a>Ottenere i dati di elevazione in base alla posizione delle coordinate

Nella pagina Web di esempio seguente viene illustrato come utilizzare il controllo mappa per visualizzare i dati di elevazione in un punto di coordinate. Quando l'utente trascina il marcatore, nella mappa vengono visualizzati i dati di elevazione in un popup.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Ottenere l'elevazione nella posizione" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la pagina relativa all' <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>elevazione dei privilegi nella posizione</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Ottenere i dati di elevazione per rettangolo di delimitazione

Nella pagina Web di esempio seguente viene illustrato come utilizzare il controllo mappa per visualizzare i dati di elevazione contenuti in un rettangolo di delimitazione. L'utente definisce il rettangolo di selezione facendo clic sull' `square` icona nell'angolo in alto a sinistra e disegnando il quadrato in un punto qualsiasi della mappa. Il controllo mappa eseguirà quindi il rendering dei dati di elevazione in base ai colori specificati nella chiave che si trova nell'angolo superiore destro.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Innalzamento di livello in base al rettangolo di delimitazione" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere le elevazioni delle penne in base al rettangolo di <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>delimitazione</a> di mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Ottenere i dati di elevazione per percorso polilinea

Nella pagina Web di esempio seguente viene illustrato come utilizzare il controllo mappa per visualizzare i dati di elevazione lungo un tracciato. L'utente definisce il percorso facendo clic sull' `Polyline` icona nell'angolo in alto a sinistra e disegnando la polilinea sulla mappa. Il controllo mappa esegue quindi il rendering dei dati di elevazione nei colori specificati nella chiave che si trova nell'angolo superiore destro.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gradiente percorso di elevazione" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>sfumatura del percorso di elevazione</a> delle penne da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente le API di elevazione delle mappe di Azure (anteprima), vedere:

> [!div class="nextstepaction"]
> [Elevazione (anteprima)-recuperare i dati per le coordinate longitudinali Lat](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Elevazione (anteprima)-recuperare i dati per il rettangolo di delimitazione](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevazione (anteprima)-recuperare i dati per la polilinea](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2 – ottenere il riquadro Mappa](/rest/api/maps/renderv2)

Per un elenco completo delle API REST di Mappe di Azure, vedere:

> [!div class="nextstepaction"]
> [API REST di Mappe di Azure](/rest/api/maps/)