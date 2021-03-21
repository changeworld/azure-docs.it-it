---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89570218"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="Flusso di segnali":::

Il diagramma riportato sopra mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](../../../media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di rilevamento del movimento](../../../media-graph-concept.md#motion-detection-processor). L'origine RTSP invia gli stessi fotogrammi video a un nodo del [processore di controllo del segnale](../../../media-graph-concept.md#signal-gate-processor), che rimane chiuso finché non viene attivato da un evento.

Quando il processore di rilevamento del movimento rileva un movimento nel video, invia un evento al nodo del processore di controllo del segnale, attivandolo. Il controllo si apre per il periodo di tempo configurato, inviando i fotogrammi video al nodo [sink di file](../../../media-graph-concept.md#file-sink). Il nodo sink registra il video come file MP4 nel file system locale del dispositivo perimetrale. Il file viene salvato nel percorso configurato.

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.
