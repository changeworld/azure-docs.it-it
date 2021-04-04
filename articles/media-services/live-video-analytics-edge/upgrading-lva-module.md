---
title: Aggiornamento di analisi video in tempo reale IoT Edge da 1,0 a 2,0
description: Questo articolo descrive le differenze e i diversi aspetti da considerare durante l'aggiornamento di Live Video Analytics (LVA) nel modulo Azure IoT Edge.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955650"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Aggiornamento di analisi video in tempo reale IoT Edge da 1,0 a 2,0

Questo articolo descrive le differenze e i diversi aspetti da considerare durante l'aggiornamento di Live Video Analytics (LVA) nel modulo Azure IoT Edge.

## <a name="change-list"></a>Elenco modifiche

> [!div class="mx-tdCol4BreakAll"]
> |Titolo|Analisi video Live 1,0|Analisi video Live 2,0|Descrizione|
> |-------------|----------|---------|---------|
> |Immagine del contenitore|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Immagini Docker pubblicate da Microsoft per analisi video in tempo reale su Azure IoT Edge|
> |**Nodi MediaGraph** |    |   |   |
> |Origini|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Origine RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Origine messaggio dell'hub Internet |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Origine RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Origine messaggio dell'hub Internet | Nodi MediaGraph che fungono da origini per l'inserimento e i messaggi multimediali.|
> |Processori|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore rilevamento movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore filtro frequenza frame </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore di estensione http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore di estensione Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore Gate Signal |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore rilevamento movimento </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Processore filtro frequenza frame**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore di estensione http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore di estensione Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processore Gate Signal | Nodi MediaGraph che consentono di formattare i file multimediali prima di inviarli ai server di inferenza di intelligenza artificiale.|
> |Sink|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sink di asset </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sink di file </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sink messaggi dell'hub Internet|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sink di asset </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sink di file </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sink messaggi dell'hub Internet| Nodi MediaGraph che consentono di archiviare il supporto elaborato.|
> |**MediaGraphs supportati** |    |   |   |
> |Topologie|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione video continua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi del movimento e registrazione video continua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi esterna e registrazione video continua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione basata su eventi in movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione basata su eventi in intelligenza artificiale</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione basata su eventi in un evento esterno </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi del movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi del movimento seguita da intelligenza artificiale |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione video continua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi del movimento e registrazione video continua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi esterna e registrazione video continua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione basata su eventi in movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione basata su eventi in intelligenza artificiale</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Registrazione basata su eventi in un evento esterno </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi del movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analisi del movimento seguita da intelligenza artificiale </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Composizione intelligenza artificiale** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Registrazione audio e video** </br>  | Topologie MediaGraph che consentono di definire il progetto di un grafico, con parametri come segnaposto per i valori.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Aggiornamento di analisi video in tempo reale sul modulo IoT Edge da 1,0 a 2,0
Quando si aggiorna l'analisi video in tempo reale sul modulo IoT Edge, assicurarsi di aggiornare le informazioni seguenti.
### <a name="container-image"></a>Immagine del contenitore
Nel modello di distribuzione cercare l'analisi video in tempo reale sul modulo IoT Edge nel `modules` nodo e aggiornare il `image` campo come:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Se non è stato modificato il nome dell'analisi video in tempo reale sul modulo IoT Edge, cercare `lvaEdge` nel nodo del modulo.

### <a name="topology-file-changes"></a>Modifiche al file di topologia
Nei file della topologia assicurarsi che **`apiVersion`** sia impostato su 2,0

### <a name="mediagraph-node-changes"></a>Modifiche al nodo MediaGraph


* L'audio dall'origine della fotocamera ora può essere passato a valle insieme a video. È possibile passare solo **audio** **o video** o **audio e video** con l'aiuto di **`outputSelectors`** a qualsiasi nodo grafico. Ad esempio, se si vuole selezionare solo video dall'origine RTSP e passarlo a valle, aggiungere quanto segue al nodo di origine RTSP:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** È una proprietà facoltativa. Se non viene usato, il grafico multimediale passerà l'audio (se abilitata) e il video dalla fotocamera RTSP downstream. 

* Nei `MediaGraphHttpExtension` `MediaGraphGrpcExtension` processori e tenere presente le seguenti modifiche:  
    #### <a name="image-properties"></a>Proprietà dell'immagine
    * `MediaGraphImageFormatEncoded` non è più supportato. 
      * Usare invece **`MediaGraphImageFormatBmp`** o **`MediaGraphImageFormatJpeg`** o **`MediaGraphImageFormatPng`** . Ad esempio,
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Se si desidera utilizzare le immagini RAW, utilizzare **`MediaGraphImageFormatRaw`** . Per usarlo, aggiornare il nodo immagine come:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > I valori possibili di pixelFormat includono: `yuv420p` ,, `rgb565be` `rgb565le` , `rgb555be` , `rgb555le` , `rgb24` , `bgr24` , `argb` , `rgba` , `abgr` , `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>extensionConfiguration per processore di estensioni Grpc  
    * Nel `MediaGraphGrpcExtension` processore è disponibile una nuova proprietà denominata **`extensionConfiguration`** , che è una stringa facoltativa che può essere usata come parte del contratto gRPC. Questo campo può essere utilizzato per passare i dati al server di inferenza ed è possibile definire la modalità di utilizzo dei dati da parte del server di inferenza.  
    Un caso d'uso di questa proprietà è quando si dispone di più modelli di intelligenza artificiale in un unico server di inferenza. Con questa proprietà non sarà necessario esporre un nodo per ogni modello di intelligenza artificiale. Al contrario, per un'istanza di Graph, come provider di estensioni, è possibile definire la modalità di selezione dei diversi modelli di intelligenza artificiale usando la **`extensionConfiguration`** proprietà e durante l'esecuzione, LVA passerà questa stringa al server di inferenza, che può usare questo per richiamare il modello di intelligenza artificiale desiderato.  

    #### <a name="ai-composition"></a>Composizione intelligenza artificiale
    * Analisi video Live 2,0 supporta ora l'uso di più processori di estensione del grafico multimediale all'interno di una topologia. È possibile passare i frame multimediali dalla fotocamera RTSP a diversi modelli di intelligenza artificiale in sequenza, in parallelo o in una combinazione di entrambi. Vedere una topologia di esempio che mostra due modelli di intelligenza artificiale usati in modo sequenziale.

### <a name="disk-space-management-with-sink-nodes"></a>Gestione dello spazio su disco con nodi sink
* Nel nodo **sink di file** è ora possibile specificare la quantità di spazio IOT Edge su disco che il modulo di analisi video live può usare per archiviare le immagini elaborate. A tale scopo, aggiungere il **`maximumSizeMiB`** campo al nodo FileSink. Di seguito è riportato un nodo sink di file di esempio:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* Nel nodo **sink di asset** è possibile specificare la quantità di spazio IOT Edge su disco che il modulo di analisi video live può usare per archiviare le immagini elaborate. A tale scopo, aggiungere il **`localMediaCacheMaximumSizeMiB`** campo al nodo del sink di asset. Un nodo di sink di asset di esempio è il seguente:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  Il percorso del **sink di file** è suddiviso in base al percorso della directory e al modello di nome file, mentre il percorso del **sink di asset** include il percorso della directory di base.  

### <a name="frame-rate-management"></a>Gestione frequenza dei fotogrammi
* **`MediaGraphFrameRateFilterProcessor`** è deprecato in **analisi video live sul modulo IoT Edge 2,0** .
    * Per campionare il video in arrivo per l'elaborazione, aggiungere la **`samplingOptions`** proprietà ai processori di estensione MediaGraph ( `MediaGraphHttpExtension` o `MediaGraphGrpcExtension` )  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Metriche dei moduli nel formato Prometeo con Telegraf
Con questa versione, è possibile usare Telegraf per inviare le metriche a monitoraggio di Azure. A questo punto, è possibile indirizzare le metriche a Log Analytics o a un hub eventi.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Tassonomia degli eventi":::

È possibile creare facilmente un'immagine Telegraf con una configurazione personalizzata usando docker. Ulteriori informazioni sono disponibili nella pagina [monitoraggio e registrazione](monitoring-logging.md#azure-monitor-collection-via-telegraf) .

## <a name="next-steps"></a>Passaggi successivi

[Inizia a usare analisi video in tempo reale IoT Edge](get-started-detect-motion-emit-events-quickstart.md)