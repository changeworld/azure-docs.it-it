---
title: Visualizzare i dati sul traffico nelle mappe Android | Mappe Microsoft Azure
description: In questo articolo si apprenderà come visualizzare i dati sul traffico su una mappa usando il Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aabe246c343537a42c33d3eaad0bfae3989022fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604516"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Mostra i dati sul traffico sulla mappa (Android SDK)

I dati di flusso e gli eventi imprevisti sono i due tipi di dati di traffico che possono essere visualizzati sulla mappa. Questa guida illustra come visualizzare entrambi i tipi di dati di traffico. I dati degli eventi imprevisti sono costituiti da dati basati su punti e righe per operazioni quali costruzioni, chiusure stradali e incidenti. I dati di Flow mostrano le metriche relative al flusso di traffico in viaggio.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di completare la procedura descritta nella [Guida introduttiva: creare un documento dell'app Android](quick-android-map.md) . I blocchi di codice in questo articolo possono essere inseriti nel `onReady` gestore eventi maps.

## <a name="show-traffic-on-the-map"></a>Mostrare il traffico sulla mappa

In Mappe di Azure sono disponibili due tipi di dati di traffico:

- Dati di eventi: dati basati su linee e punti per segnalare cantieri, chiusure stradali e incidenti.
- Flussi di dati: metriche sul flusso di traffico sulle strade. I dati relativi al flusso di traffico vengono spesso usati per colorare le strade. I colori dipendono dalla misura in cui il traffico sta rallentando il flusso stradale, rispetto al limite di velocità o a un'altra metrica. Sono disponibili quattro valori che possono essere passati nell' `flow` opzione relativa al traffico della mappa.

    |Valore del flusso | Descrizione|
    | :-- | :-- |
    | TrafficFlow. NONE | Non Visualizza i dati sul traffico sulla mappa |
    | TrafficFlow. relativa | Mostra i dati sul traffico relativi alla velocità del flusso libero della strada |
    | TrafficFlow.RELATIVE_DELAY | Visualizza le aree più lente rispetto al ritardo medio previsto |
    | TrafficFlow. ABSOLUTe | Mostra la velocità assoluta di tutti i veicoli in viaggio |

Il codice seguente illustra come visualizzare i dati relativi al traffico sulla mappa.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

Lo screenshot seguente mostra il codice precedente che esegue il rendering delle informazioni sul traffico in tempo reale sulla mappa.

![Mappa che mostra le informazioni sul traffico in tempo reale](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Ottenere i dettagli dell'evento imprevisto del traffico

I dettagli relativi a un evento imprevisto del traffico sono disponibili nelle proprietà della funzionalità utilizzata per visualizzare l'evento imprevisto sulla mappa. Gli eventi imprevisti del traffico vengono aggiunti alla mappa usando il servizio di riquadri del vettore di Azure Maps Traffic Incident. Il formato dei dati nei riquadri vettoriali, se [documentati qui](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Il codice seguente aggiunge un evento click alla mappa e recupera la funzionalità per gli eventi imprevisti del traffico su cui è stato fatto clic e visualizza un messaggio di avviso popup con alcuni dettagli.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

Lo screenshot seguente mostra il codice precedente che esegue il rendering delle informazioni sul traffico in tempo reale sulla mappa con un messaggio di avviso popup che Visualizza i dettagli dell'evento imprevisto.

![Mappa che mostra le informazioni sul traffico in tempo reale con un messaggio popup che Visualizza i dettagli dell'evento](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiungere altri dati alla mappa, vedere le guide seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](how-to-add-tile-layer-android-map.md)
