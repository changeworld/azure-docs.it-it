---
ms.openlocfilehash: c4c58724f8ba2accd827e1a1b3819e40210aa9c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97486881"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Flusso di segnali":::

Il diagramma mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](../../../media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di estensioni HTTP](../../../media-graph-concept.md#http-extension-processor). 

Il nodo di estensioni HTTP svolge il ruolo di proxy. Campiona i fotogrammi video in ingresso impostati tramite il campo `samplingOptions` e li converte nel tipo di immagine specificato. Quindi inoltra l'immagine su REST a un altro modulo Edge che esegue un modello di intelligenza artificiale dietro un endpoint HTTP. In questo esempio, il modulo Edge viene creato usando il modello [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), che è in grado di rilevare molti tipi di oggetti. Il nodo del processore di estensioni HTTP raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo [sink dell'hub IoT](../../../media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.
