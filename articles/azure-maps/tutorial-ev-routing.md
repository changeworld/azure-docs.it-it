---
title: 'Esercitazione: Pianificare itinerari per veicoli elettrici con Azure Notebooks (Python) con Mappe di Microsoft Azure'
description: Esercitazione su come pianificare itinerari per veicoli elettrici usando le API di pianificazione di itinerari di Mappe di Microsoft Azure e Azure Notebooks
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680333"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Esercitazione: Pianificare itinerari per veicoli elettrici con Azure Notebooks (Python)

Mappe di Azure è un portfolio di API di servizi geospaziali integrate in modalità nativa in Azure che consentono a sviluppatori, aziende e ISV di creare app con riconoscimento della posizione e soluzioni IoT, di mobilità, di logistica e di rintracciamento asset. 

Le API REST di Mappe di Azure possono essere chiamate da linguaggi come Python e R per scenari di analisi di dati geospaziali e Machine Learning. Mappe di Azure include un set completo di [API di pianificazione di itinerari](/rest/api/maps/route) che consente di calcolare gli itinerari tra diversi punti dati in base a varie condizioni, come tipo di veicolo o area raggiungibile. 

Questa esercitazione illustra come aiutare un autista la cui batteria elettrica è quasi scarica. L'autista deve trovare la stazione di ricarica più vicina possibile alla posizione del veicolo.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare ed eseguire un file di Jupyter Notebook in [Azure Notebooks](https://notebooks.azure.com) nel cloud.
> * Chiamare le API REST di Mappe di Azure in Python.
> * Cercare un'area raggiungibile in base al modello di consumo del veicolo elettrico.
> * Cercare le stazioni di ricarica per veicoli elettrici entro l'area raggiungibile, o isocrona.
> * Eseguire il rendering dei limiti dell'area raggiungibile e delle stazioni di ricarica su una mappa.
> * Trovare e visualizzare un itinerario per la stazione di ricarica di veicoli elettrici più vicina in base al tempo di guida.


## <a name="prerequisites"></a>Prerequisiti 

Per completare questa esercitazione, è prima di tutto necessario creare un account di Mappe di Azure e ottenere la chiave primaria (chiave di sottoscrizione). 

Per creare una sottoscrizione dell'account Mappe di Azure, seguire le istruzioni riportate in [Creare un account](quick-demo-map-app.md#create-an-azure-maps-account). È necessaria una sottoscrizione dell'account Mappe di Azure con il piano tariffario S1. 

Per ottenere la chiave di sottoscrizione primaria per l'account, seguire le istruzioni riportate in [Ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Creare un progetto Azure Notebooks

Per seguire questa esercitazione, è necessario creare un progetto di Azure Notebooks, quindi scaricare ed eseguire il file di Jupyter Notebook. Il file di Jupyter Notebook contiene codice Python, che implementa lo scenario dell'esercitazione. Per creare un progetto di Azure Notebooks e caricarvi il documento di Jupyter Notebook, seguire questa procedura:

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per altre informazioni, vedere [Avvio rapido: Accedere e impostare un ID utente](https://notebooks.azure.com).
1. Nella parte superiore della pagina del profilo pubblico selezionare **Progetti personali**.

    ![Pulsante Progetti personali](./media/tutorial-ev-routing/myproject.png)

1. Nella pagina **Progetti personali** selezionare **Nuovo progetto**.
 
   ![Pulsante Nuovo progetto](./media/tutorial-ev-routing/create-project.png)

1. Nel riquadro **Crea nuovo progetto** immettere un nome per il progetto e l'ID di progetto.
 
    ![Riquadro Crea nuovo progetto](./media/tutorial-ev-routing/create-project-window.png)

1. Selezionare **Create** (Crea).

1. Dopo aver creato il progetto, scaricare il [file del documento di Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) dal [repository di Jupyter Notebook per Mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Selezionare il progetto nell'elenco della pagina **Progetti personali**, quindi selezionare **Carica** per caricare il file del documento di Jupyter Notebook. 

    ![Caricare il file di Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Caricare il file dal computer e selezionare **Fatto**.

1. Una volta completato il caricamento, il file viene visualizzato nella pagina del progetto. Fare doppio clic sul file per aprirlo in formato Jupyter Notebook.

Esplorare la funzionalità implementata nel file di Jupyter Notebook. Eseguire il codice nel file di Jupyter Notebook, una cella alla volta. Per eseguire il codice in ogni cella, selezionare il pulsante **Esegui** nella parte superiore dell'app Jupyter Notebook.

  ![Pulsante Esegui](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installare i pacchetti a livello di progetto

Per eseguire il codice in Jupyter Notebook, installare i pacchetti a livello di progetto seguendo questa procedura:

1. Scaricare il file [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) dal [repository di Jupyter Notebook per Mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) e quindi caricarlo nel progetto.
1. Nel dashboard del progetto selezionare **Project Settings** (Impostazioni progetto). 
1. Nel riquadro **Project Settings** (Impostazioni progetto) selezionare la scheda **Environment** (Ambiente) e quindi **Add** (Aggiungi).
1. In **Environment Setup Steps** (Passaggi di configurazione dell'ambiente) procedere come segue:   
    a. Nel primo elenco a discesa scegliere **Requirements.txt**.  
    b. Nel secondo elenco a discesa scegliere il file *requirements.txt*.  
    c. Nel terzo elenco a discesa scegliere **Python versione 3.6** come versione.
1. Selezionare **Salva**.

    ![Installare i pacchetti](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Caricare i moduli e i framework necessari

Per caricare tutti i moduli e i framework necessari, eseguire lo script seguente.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Richiesta dei limiti dell'area raggiungibile

Una società di spedizioni ha alcuni veicoli elettrici nella propria flotta. Durante la giornata, i veicoli elettrici devono essere ricaricati senza la necessità di tornare nel magazzino. Ogni volta che la carica rimanente diminuisce a meno di un'ora di autonomia, si cerca una serie di stazioni di ricarica che rientrano in un'area raggiungibile. Essenzialmente, si cerca una stazione di ricarica quando la batteria è quasi scarica. Inoltre, si ottengono informazioni sui limiti per tale area di stazioni di ricarica. 

Poiché la società preferisce usare itinerari che richiedano un equilibrio tra costi contenuti e velocità, la richiesta di routeType è *eco*. Lo script seguente chiama l'[API Get Route Range](/rest/api/maps/route/getrouterange) del servizio di routing di Mappe di Azure usando i parametri per il modello di consumo del veicolo. Lo script analizza quindi la risposta per creare un oggetto poligono del formato geoJSON, che rappresenta l'area di raggiungibilità massima dell'auto.

Per determinare i limiti dell'area raggiungibile del veicolo elettrico. eseguire lo script nella cella seguente:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Cercare le stazioni di ricarica per veicoli elettrici entro l'area raggiungibile

Dopo aver determinato l'area raggiungibile (isocrona) per il veicolo elettrico, è possibile cercare le stazioni di ricarica in tale area. 

Lo script seguente chiama l'[API Post Search Inside Geometry](/rest/api/maps/search/postsearchinsidegeometry) di Mappe di Azure Cerca le stazioni di ricarica per veicoli elettrici entro i limiti di distanza massima raggiungibile dell'auto. Lo script analizza quindi la risposta in una matrice di posizioni raggiungibili.

Per cercare stazioni di ricarica per veicoli elettrici entro l'area raggiungibile, eseguire lo script seguente:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Caricare l'area raggiungibile e i punti di ricarica nel servizio dati di Mappe di Azure (anteprima)

È possibile visualizzare su una mappa le stazioni di ricarica e i limiti dell'area massima raggiungibile dal veicolo elettrico. A tale scopo, caricare i dati relativi ai limiti e alle stazioni di ricarica come oggetti geojson nel servizio dati di Mappe di Azure (anteprima). Usare l'[API Data Upload](/rest/api/maps/data/uploadpreview). 

Per caricare i dati dei limiti e dei punti di ricarica nel servizio dati di Mappe di Azure, eseguire le due celle seguenti:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Eseguire il rendering delle stazioni di ricarica e dell'area raggiungibile su una mappa

Dopo aver caricato i dati nel servizio dati, chiamare il servizio [Get Map Image](/rest/api/maps/render/getmapimage) di Mappe di Azure. Questo servizio viene usato per eseguire il rendering dei punti di ricarica e dei limiti massimi raggiungibili sull'immagine della mappa statica eseguendo lo script seguente:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Mappa che mostra l'area delle posizioni](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Trovare la stazione di ricarica ottimale

Per prima cosa, è necessario individuare tutte le possibili stazioni di ricarica entro l'area raggiungibile. Quindi, si vorrà stabilire quale di esse è possibile raggiungere nel minor tempo. 

Lo script seguente chiama l'[API Matrix Routing](/rest/api/maps/route/postroutematrix) di Mappe di Azure. Restituisce la posizione del veicolo specificata, il tempo di viaggio e la distanza da ogni stazione di ricarica. Lo script nella cella successiva analizza la risposta per trovare la stazione di ricarica raggiungibile più vicina rispetto al tempo.

Per trovare la stazione di ricarica raggiungibile più vicina che è possibile raggiungere nella quantità di tempo minima, eseguire la cella seguente:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Calcolare l'itinerario per la stazione di ricarica più vicina

Dopo aver trovato la stazione di ricarica più vicina, è possibile chiamare l'[API Get Route Directions](/rest/api/maps/route/getroutedirections) per richiedere l'itinerario dettagliato dalla posizione corrente del veicolo elettrico alla stazione di ricarica.

Per ottenere l'itinerario verso la stazione di ricarica e analizzare la risposta per creare un oggetto geoJSON che lo rappresenta, eseguire lo script nella cella seguente:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualizzare l'itinerario

Per visualizzare l'itinerario, caricare prima di tutto i relativi dati come oggetto geojson nel servizio dati di Mappe di Azure (anteprima). A tale scopo, usare l'[API Data Upload](/rest/api/maps/data/uploadpreview) di Mappe di Azure. Quindi chiamare il servizio di rendering, l'[API Get Map Image](/rest/api/maps/render/getmapimage), per eseguire il rendering dell'itinerario sulla mappa e visualizzarlo.

Per ottenere un'immagine dell'itinerario sottoposto a rendering sulla mappa, eseguire lo script seguente:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Mappa che mostra l'itinerario](./media/tutorial-ev-routing/route.png)

In questa esercitazione si è appreso come chiamare direttamente le API REST di Mappe di Azure e visualizzare i dati di Mappe di Azure con Python.

Per esplorare le API di Mappe di Azure usate in questa esercitazione, vedere:

* [Get Route Range](/rest/api/maps/route/getrouterange)
* [Post Search Inside Geometry](/rest/api/maps/search/postsearchinsidegeometry)
* [Data Upload](/rest/api/maps/data/uploadpreview)
* [Render - Get Map Image](/rest/api/maps/render/getmapimage)
* [Post Route Matrix](/rest/api/maps/route/postroutematrix)
* [Get Route Directions](/rest/api/maps/route/getroutedirections)
* [API REST di Mappe di Azure](./consumption-model.md)

## <a name="clean-up-resources"></a>Pulire le risorse

Non sono presenti risorse che richiedono la pulizia.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Notebooks, vedere

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)