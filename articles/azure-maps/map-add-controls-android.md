---
title: Aggiungere controlli a una mappa Android | Mappe Microsoft Azure
description: Come aggiungere il controllo zoom, il controllo pitch, ruotare il controllo e una selezione stile a una mappa in Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8224192ed0d13af2ff6ac60aac5aa928589ff01a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054889"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Aggiungere controlli a una mappa (Android SDK)

Questo articolo illustra come aggiungere controlli dell'interfaccia utente alla mappa.

## <a name="add-zoom-control"></a>Aggiungere un controllo zoom

Un controllo zoom aggiunge pulsanti per lo zoom avanti e indietro della mappa. L'esempio di codice seguente crea un'istanza della `ZoomControl` classe e la aggiunge a una mappa.

```java
map.controls.add(new ZoomControl());
```

Lo screenshot seguente è un controllo zoom caricato su una mappa.

![Controllo zoom aggiunto alla mappa](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Aggiungere un controllo inclinazione

Un controllo pitch aggiunge i pulsanti per inclinare il pitch per eseguire il mapping rispetto all'orizzonte. L'esempio di codice seguente crea un'istanza della `PitchControl` classe e la aggiunge a una mappa.

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

Lo screenshot seguente è un controllo pitch caricato su una mappa.

![Controllo pitch aggiunto alla mappa](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Aggiungere un controllo bussola

Un controllo Compass aggiunge un pulsante per la rotazione della mappa. L'esempio di codice seguente crea un'istanza della `CompassControl` classe e la aggiunge a una mappa.

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

Lo screenshot seguente è un controllo Compass caricato su una mappa.

![Controllo Compass aggiunto alla mappa](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Aggiungi controllo del traffico

Un controllo del traffico aggiunge un pulsante per l'attivazione/disattivazione della visibilità dei dati del traffico sulla mappa. L'esempio di codice seguente crea un'istanza della `TrafficControl` classe e la aggiunge a una mappa.

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

La schermata seguente è un controllo del traffico caricato su una mappa.

![Controllo del traffico aggiunto alla mappa](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Una mappa con tutti i controlli

È possibile inserire più controlli in una matrice e aggiungerli contemporaneamente alla mappa e posizionarli nella stessa area della mappa per semplificare lo sviluppo. Nell'esempio seguente vengono aggiunti i controlli di navigazione standard alla mappa utilizzando questo approccio.

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

La schermata seguente mostra tutti i controlli caricati su una mappa. Si noti che l'ordine in cui vengono aggiunti alla mappa è l'ordine in cui verranno visualizzati.

![Tutti i controlli aggiunti a map](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)
