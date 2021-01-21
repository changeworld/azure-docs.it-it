---
title: Introduzione alle funzioni geospaziali di Analisi di flusso di Azure
description: Questo articolo descrive le funzioni geospaziali che vengono usate nei processi di Analisi di flusso di Azure.
author: krishna0815
ms.author: krishmam
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: dc590593b9bff8f646ee6155d32a2ce3f9790f6e
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625249"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introduzione alle funzioni geospaziali di Analisi di flusso

Le funzioni geospaziali in Analisi di flusso di Azure abilitano l'analisi in tempo reale sul flusso di dati geospaziali. Con poche righe di codice, è possibile sviluppare una soluzione di livello di produzione per scenari complessi. Queste funzioni supportano tutti i tipi di WKT e i punti GeoJSON, Polygon e LineString.

Degli esempi di scenari che possono trarre vantaggio da funzioni geospaziali comprendono:

* Condivisione delle corse
* Gestione della flotta
* Verifica delle risorse
* Definizione del geo-fencing
* Rilevamento telefonico tra siti di cella

Linguaggio di query di analisi di flusso ha sette funzioni geospaziali incorporati: **CreateLineString**, **Createpoint(20.0**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**, e **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

La funzione `CreateLineString` accetta i punti e restituisce un oggetto GeoJSON LineString, che può essere tracciato come una riga in una mappa. È necessario avere almeno due punti per creare una LineString. I punti di LineString verranno connessi in ordine.

La query seguente usa `CreateLineString` per creare una LineString che usa tre punti. Il primo punto viene creato dall'esecuzione del flusso di dati di input, mentre gli altri due vengono creati manualmente.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Esempio di input  
  
|latitudine|longitudine|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Esempio di output  

 {"type" : "LineString", "coordinate" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinate" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Per altre informazioni, visitare il riferimento [CreateLineString](/stream-analytics-query/createlinestring).

## <a name="createpoint"></a>CreatePoint

La funzione `CreatePoint` accetta una latitudine e longitudine e restituisce un punto GeoJSON, che può essere tracciato su una mappa. I termini di latitudine e longitudine devono essere un tipo di dati **float**.

La query di esempio seguente usa `CreatePoint` per creare un punto che usa termini di latitudine e longitudine dall'esecuzione del flusso di dati di input.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Esempio di input  
  
|latitudine|longitudine|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Esempio di output
  
 {"type": "Point", "coordinate": [-10.2, 3.0]}  
  
 {"type": "Point", "coordinate": [20.2321, -87.33]}  

Per altre informazioni, visitare il riferimento [CreatePoint](/stream-analytics-query/createpoint).

## <a name="createpolygon"></a>CreatePolygon

La funzione `CreatePolygon` accetta i punti e restituisce un record di poligono GeoJSON. L'ordine dei punti deve seguire l'orientamento dell'anello a destra, o in senso antiorario. Si immagini da andare da un punto a un altro nell'ordine in cui sono stati dichiarati. Il centro del poligono sarebbe quello di sinistra tutto il tempo.

La query di esempio seguente usa `CreatePolygon` per creare un'istanza del poligono da tre punti. I primi due punti vengono creati manualmente e l'ultimo punto viene creato dai dati di input.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Esempio di input  
  
|latitudine|longitudine|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Esempio di output  

 {"type" : "Polygon", "coordinate" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinate" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Per altre informazioni, visitare il riferimento [CreatePolygon](/stream-analytics-query/createpolygon).


## <a name="st_distance"></a>ST_DISTANCE
La `ST_DISTANCE` funzione restituisce la distanza tra due geometrie in metri. 

La query seguente utilizza `ST_DISTANCE` per generare un evento quando una stazione di rifornimento è a meno di 10 km dalla propria auto.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Per altre informazioni, visitare il riferimento [ST_DISTANCE](/stream-analytics-query/st-distance).

## <a name="st_overlaps"></a>ST_OVERLAPS
La `ST_OVERLAPS` funzione Confronta due geometrie. Se le geometrie si sovrappongono, la funzione restituisce 1. La funzione restituisce 0 se le geometrie non si sovrappongono. 

La query seguente usa `ST_OVERLAPS` per generare un evento quando una compilazione è all'interno di una possibile zona di flood.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

La query di esempio seguente genera un evento quando un temporale è diretto verso un'automobile.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Per altre informazioni, visitare il riferimento [ST_OVERLAPS](/stream-analytics-query/st-overlaps).

## <a name="st_intersects"></a>ST_INTERSECTS
La `ST_INTERSECTS` funzione Confronta due geometrie. Se le geometrie si intersecano, la funzione restituisce 1. La funzione restituisce 0 se le geometrie non si intersecano tra loro.

La query di esempio seguente usa `ST_INTERSECTS` per determinare se una strada asfaltata interseca una strada sterrata.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Esempio di input  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"Type": "LineString", "coordinates": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString", "coordinates": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"Type": "LineString", "coordinates": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString", "coordinates": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Esempio di output  

 1  
  
 0  

Per altre informazioni, visitare il riferimento [ST_INTERSECTS](/stream-analytics-query/st-intersects).

## <a name="st_within"></a>ST_WITHIN
La `ST_WITHIN` funzione determina se una geometria si trova all'interno di un'altra geometria. Se il primo è contenuto nell'ultimo, la funzione restituirà 1. La funzione restituirà 0 se la prima geometria non si trova all'interno dell'ultima.

La query di esempio seguente usa `ST_WITHIN` per determinare se il punto di destinazione di recapito è all'interno del poligono di warehouse specificato.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Esempio di input  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{"Type": "Point", "coordinates": [76,6, 10,1]}|{"Type": "Polygon", "coordinates": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "Point", "coordinates": [15,0, 15,0]}|{"Type": "Polygon", "coordinates": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Esempio di output  

 0  
  
 1  

Per altre informazioni, visitare il riferimento [ST_WITHIN](/stream-analytics-query/st-within).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)
