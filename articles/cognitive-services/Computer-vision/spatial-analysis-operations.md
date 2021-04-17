---
title: Operazioni di analisi spaziale
titleSuffix: Azure Cognitive Services
description: Operazioni di analisi spaziale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 37ac7573a1794c97c81fe5364204f85ff14d9fa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538085"
---
# <a name="spatial-analysis-operations"></a>Operazioni di analisi spaziale

L'analisi spaziale consente l'analisi dei video in streaming in tempo reale dai dispositivi della fotocamera. Per ogni dispositivo fotocamera configurato, le operazioni per l'analisi spaziale genereranno un flusso di output di messaggi JSON inviati all'istanza di hub IoT di Azure. 

Il contenitore Analisi spaziale implementa le operazioni seguenti:

| Identificatore dell'operazione| Descrizione|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Conta le persone in una zona designata nel campo di visualizzazione della fotocamera. La zona deve essere completamente coperta da una singola fotocamera per consentire a PersonCount di registrare un totale accurato. <br> Genera un evento _personCountEvent iniziale_ e quindi _eventi personCountEvent_ quando il conteggio cambia.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Tiene traccia del momento in cui una persona attraversa una linea designata nel campo di visualizzazione della fotocamera. <br>Genera un _evento personLineEvent_ quando la persona supera la linea e fornisce informazioni direzionali. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Genera un evento _personZoneEnterExitEvent_ quando una persona entra o esce dalla zona e fornisce informazioni direzionali con il lato numerato della zona attraversata. Genera un _evento personZoneDwellTimeEvent_ quando la persona esce dalla zona e fornisce informazioni direzionali, nonché il numero di millisecondi trascorsi dalla persona all'interno della zona. |
| cognitiveservices.vision.spatialanalysis-persondistance | Tiene traccia di quando gli utenti violano una regola di distanza. <br> Genera periodicamente _un evento personDistanceEvent_ con la posizione di ogni violazione della distanza. |
| cognitiveservices.vision.spatialanalysis | Operazione generica che può essere usata per eseguire tutti gli scenari indicati in precedenza. Questa opzione è più utile quando si vogliono eseguire più scenari sulla stessa fotocamera o usare le risorse di sistema (ad esempio GPU) in modo più efficiente. |

Tutte le operazioni precedenti sono disponibili anche nella versione , che consente di visualizzare i fotogrammi video durante `.debug` l'elaborazione. Sarà necessario eseguire nel `xhost +` computer host per abilitare la visualizzazione di fotogrammi video ed eventi.

| Identificatore dell'operazione| Descrizione|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Conta le persone in una zona designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personCountEvent iniziale_ e quindi _eventi personCountEvent_ quando il conteggio cambia.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Tiene traccia del momento in cui una persona attraversa una linea designata nel campo di visualizzazione della fotocamera. <br>Genera un _evento personLineEvent_ quando la persona supera la linea e fornisce informazioni direzionali. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Genera un evento _personZoneEnterExitEvent_ quando una persona entra o esce dalla zona e fornisce informazioni direzionali con il lato numerato della zona attraversata. Genera un _evento personZoneDwellTimeEvent_ quando la persona esce dalla zona e fornisce informazioni direzionali, nonché il numero di millisecondi trascorsi dalla persona all'interno della zona. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Tiene traccia di quando gli utenti violano una regola di distanza. <br> Genera periodicamente _un evento personDistanceEvent_ con la posizione di ogni violazione della distanza. |
| cognitiveservices.vision.spatialanalysis.debug | Operazione generica che può essere usata per eseguire tutti gli scenari indicati in precedenza. Questa opzione è più utile quando si vogliono eseguire più scenari sulla stessa fotocamera o usare le risorse di sistema (ad esempio, GPU) in modo più efficiente. |

L'analisi spaziale può essere eseguita anche [con Analisi video live](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) come modulo di intelligenza artificiale video. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identificatore dell'operazione| Descrizione|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Conta le persone in una zona designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personCountEvent iniziale_ e quindi eventi _personCountEvent_ quando il conteggio cambia.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Tiene traccia del momento in cui una persona attraversa una linea designata nel campo di visualizzazione della fotocamera. <br>Genera un _evento personLineEvent_ quando la persona supera la linea e fornisce informazioni direzionali. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Genera un evento _personZoneEnterExitEvent_ quando una persona entra o esce dalla zona e fornisce informazioni direzionali con il lato numerato della zona incrociata. Genera un _oggetto personZoneDwellTimeEvent_ quando la persona esce dalla zona e fornisce informazioni direzionali, nonché il numero di millisecondi trascorsi dalla persona all'interno della zona.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Tiene traccia del momento in cui le persone violano una regola di distanza. <br> Genera periodicamente _un oggetto personDistanceEvent_ con la posizione di ogni violazione della distanza. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | Operazione generica che può essere usata per eseguire tutti gli scenari indicati in precedenza. Questa opzione è più utile quando si vogliono eseguire più scenari sulla stessa fotocamera o usare le risorse di sistema (ad esempio, GPU) in modo più efficiente. |

Analisi video live sono disponibili anche nella versione (ad esempio `.debug` cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug) che consente di visualizzare i fotogrammi video come elaborati. Sarà necessario eseguire nel `xhost +` computer host per abilitare la visualizzazione dei fotogrammi video e degli eventi

> [!IMPORTANT]
> I modelli di intelligenza artificiale di visione artificiale rilevano e individuano la presenza umana nei filmati video e nell'output usando un rettangolo di selezione intorno a un corpo umano. I modelli di intelligenza artificiale non tentano di individuare le identità o i dati demografici degli individui.

Questi sono i parametri richiesti da ognuna di queste operazioni di analisi spaziale.

| Parametri delle operazioni| Descrizione|
|---------|---------|
| ID operazione | Identificatore dell'operazione della tabella precedente.|
| Enabled | Booleano: true o false|
| VIDEO_URL| URL RTSP per il dispositivo fotocamera (esempio: `rtsp://username:password@url` ). L'analisi spaziale supporta il flusso con codifica H.264 tramite RTSP, http o mp4. Video_URL può essere fornito come valore stringa base64 offuscato usando la crittografia AES e, se l'URL video viene offuscato e deve essere fornito come variabili di `KEY_ENV` `IV_ENV` ambiente. L'utilità di esempio per generare chiavi e crittografia è disponibile [qui.](/dotnet/api/system.security.cryptography.aesmanaged) |
| VIDEO_SOURCE_ID | Nome descrittivo per il dispositivo fotocamera o il flusso video. Verrà restituito con l'output JSON dell'evento.|
| VIDEO_IS_LIVE| True per i dispositivi con fotocamera; false per i video registrati.|
| VIDEO_DECODE_GPU_INDEX| GPU per decodificare il fotogramma video. Per impostazione predefinita, è 0. Deve essere uguale a in `gpu_index` altre impostazioni di configurazione del nodo, ad esempio , `VICA_NODE_CONFIG` `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Larghezza dei fotogrammi del video/flusso di input (ad esempio 1920). Si tratta di un campo facoltativo e, se specificato, il frame verrà ridimensionato in base a questa dimensione mantenendo le proporzioni.|
| DETECTOR_NODE_CONFIG | JSON che indica la GPU in cui eseguire il nodo di rilevamento. Deve essere nel formato seguente: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Configurazione JSON per la zona e la riga come descritto di seguito.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` per abilitare il rilevamento di persone che indossano maschere del viso nel flusso video, `False` per disabilitarlo. Per impostazione predefinita, questa opzione è disabilitata. Il rilevamento della maschera del viso richiede che il parametro di larghezza del video di input sia 1920. `"INPUT_VIDEO_WIDTH": 1920` L'attributo face mask non verrà restituito se le persone rilevate non si trovano davanti alla fotocamera o sono troppo distorsi. Per altre [informazioni, vedere la guida al posizionamento](spatial-analysis-camera-placement.md) della fotocamera |

### <a name="detector-node-parameter-settings"></a>Impostazioni dei parametri del nodo di rilevamento
Questo è un esempio dei parametri DETECTOR_NODE_CONFIG per tutte le operazioni di analisi spaziale.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `gpu_index` | string| Indice GPU in cui verrà eseguita questa operazione.|
| `do_calibration` | string | Indica che la calibrazione è attivata. `do_calibration`deve essere true per il **corretto funzionamento di cognitiveservices.vision.spatialanalysis-persondistance.** do_calibration è impostato su True per impostazione predefinita. |
| `enable_recalibration` | bool | Indica se la ricalibrazione automatica è attivata. Il valore predefinito è `true`.|
| `calibration_quality_check_frequency_seconds` | INT | Numero minimo di secondi tra ogni controllo di qualità per determinare se è necessaria o meno la ricalibrazione. Il valore predefinito `86400` è (24 ore). Utilizzato solo quando `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | INT | Numero minimo di secondi tra la raccolta di nuovi campioni di dati per la ricalibrazione e il controllo di qualità. Il valore predefinito `300` è (5 minuti). Utilizzato solo quando `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | INT | Numero minimo di nuovi esempi di dati da raccogliere per ogni round della raccolta di campioni. Il valore predefinito è `10`. Utilizzato solo quando `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | INT | Numero massimo di campioni di dati da archiviare quando viene calibrato il modello di fotocamera. Il valore predefinito è `1000`. Utilizzato solo quando `enable_recalibration=True` .|
| `enable_breakpad`| bool | Indica se si vuole abilitare breakpad, che viene usato per generare il dump di arresto anomalo per l'uso del debug. Lo è `false` per impostazione predefinita. Se si imposta su `true` , è necessario aggiungere anche nella parte del `"CapAdd": ["SYS_PTRACE"]` `HostConfig` contenitore `createOptions` . Per impostazione predefinita, il dump di arresto anomalo del sistema viene caricato nell'app [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter. Se si vuole caricare i dump di arresto anomalo nell'app AppCenter, è possibile eseguire l'override della variabile di ambiente con il segreto `RTPT_APPCENTER_APP_SECRET` dell'app.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Configurazione e output delle operazioni di analisi spaziale
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Configurazione della zona per cognitiveservices.vision.spatialanalysis-personcount

 Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una zona. È possibile configurare più zone per questa operazione.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `zones` | list| Elenco di zone. |
| `name` | string| Nome descrittivo per questa zona.|
| `polygon` | list| Ogni coppia di valori rappresenta x,y per i vertici di un poligono. Il poligono rappresenta le aree in cui le persone vengono rilevate o conteggiate e i punti del poligono sono basati su coordinate normalizzate (0-1), dove l'angolo superiore sinistro è (0,0, 0,0) e l'angolo inferiore destro è (1,0, 1,0).   
| `threshold` | float| Gli eventi vengono in uscita quando la persona è maggiore di questo numero di pixel all'interno della zona. |
| `type` | string| Per **cognitiveservices.vision.spatialanalysis-personcount** deve essere `count` .|
| `trigger` | string| Tipo di trigger per l'invio di un evento. I valori supportati sono per l'invio di eventi quando il conteggio cambia o per l'invio periodico di eventi, indipendentemente dal fatto che il conteggio `event` sia stato modificato o `interval` meno.
| `output_frequency` | INT | Frequenza con cui gli eventi vengono in uscita. Quando `output_frequency` = X, ogni evento X viene in uscita, ad esempio `output_frequency` = 2 indica che viene restituito ogni altro evento. è `output_frequency` applicabile sia a `event` che a `interval` . |
| `focus` | string| Posizione del punto all'interno del rettangolo di selezione della persona usato per calcolare gli eventi. Il valore dello stato attivo può essere (il footprint della persona), (il centro inferiore del rettangolo di selezione della persona), (il centro del rettangolo di selezione `footprint` `bottom_center` della `center` persona).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Configurazione linea per cognitiveservices.vision.spatialanalysis-personcrossingline

Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una riga. È possibile configurare più linee di intersezione per questa operazione.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `lines` | list| Elenco di righe.|
| `name` | string| Nome descrittivo per questa riga.|
| `line` | list| Definizione della riga. Si tratta di una linea direzionale che consente di comprendere "ingresso" e "uscita".|
| `start` | coppia di valori| Coordinate x, y per il punto iniziale della linea. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori assoluti x, y, moltiplicare questi valori con le dimensioni del frame. |
| `end` | coppia di valori| Coordinate x, y per il punto finale della linea. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori assoluti x, y, moltiplicare questi valori con le dimensioni del frame. |
| `threshold` | float| Gli eventi vengono generati quando la persona è maggiore di questo numero di pixel all'interno della zona. Il valore predefinito è 16. Si tratta del valore consigliato per ottenere la massima accuratezza. |
| `type` | string| Per **cognitiveservices.vision.spatialanalysis-personcrossingline** deve essere `linecrossing` .|
|`trigger`|string|Tipo di trigger per l'invio di un evento.<br>Valori supportati: "event": viene generato quando un utente supera la linea.|
| `focus` | string| Posizione del punto all'interno del rettangolo di selezione della persona usata per calcolare gli eventi. Il valore dello stato attivo può essere (footprint della persona), (il centro inferiore del rettangolo di selezione della persona), (il centro del rettangolo di `footprint` `bottom_center` selezione della `center` persona). Il valore predefinito è footprint.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Configurazione della zona per cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una zona. È possibile configurare più zone per questa operazione.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `zones` | list| Elenco di zone. |
| `name` | string| Nome descrittivo per questa zona.|
| `polygon` | list| Ogni coppia di valori rappresenta x,y per i vertici del poligono. Il poligono rappresenta le aree in cui le persone vengono rilevate o conteggiate. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori x, y assoluti, moltiplicare questi valori per le dimensioni del frame. 
| `threshold` | float| Gli eventi vengono in uscita quando la persona è maggiore di questo numero di pixel all'interno della zona. Il valore predefinito è 48 quando type è zonecrossing e 16 quando time è DwellTime. Questi sono i valori consigliati per ottenere la massima accuratezza.  |
| `type` | string| Per **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** deve essere `zonecrossing` o `zonedwelltime` .|
| `trigger`|string|Tipo di trigger per l'invio di un evento<br>Valori supportati: "event": vengono visualizzati quando un utente entra o esce dalla zona.|
| `focus` | string| Posizione del punto all'interno del rettangolo di selezione della persona usato per calcolare gli eventi. Il valore dello stato attivo può essere (il footprint della persona), (il centro inferiore del rettangolo di selezione della persona), (il centro del rettangolo di selezione `footprint` `bottom_center` della `center` persona). Il valore predefinito è footprint.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Configurazione della zona per cognitiveservices.vision.spatialanalysis-persondistance

Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una zona per **cognitiveservices.vision.spatialanalysis-persondistance**. È possibile configurare più zone per questa operazione.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `zones` | list| Elenco di zone. |
| `name` | string| Nome descrittivo per questa zona.|
| `polygon` | list| Ogni coppia di valori rappresenta x,y per i vertici del poligono. Il poligono rappresenta le aree in cui vengono conteggiate le persone e viene misurata la distanza tra le persone. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori assoluti x, y, moltiplicare questi valori con le dimensioni del frame. 
| `threshold` | float| Gli eventi vengono generati quando la persona è maggiore di questo numero di pixel all'interno della zona. |
| `type` | string| Per **cognitiveservices.vision.spatialanalysis-persondistance** deve essere `people_distance` .|
| `trigger` | string| Tipo di trigger per l'invio di un evento. I valori supportati sono per l'invio di eventi quando il conteggio cambia o per l'invio periodico di eventi, indipendentemente dal fatto che il `event` `interval` conteggio sia stato modificato o meno.
| `output_frequency` | INT | Frequenza con cui gli eventi vengono in uscita. Quando `output_frequency` = X, ogni evento X viene in uscita, ad esempio `output_frequency` = 2 indica che ogni altro evento viene restituito. è `output_frequency` applicabile sia a `event` che a `interval` .|
| `minimum_distance_threshold` | float| Distanza in piedi che attiverà un evento "TooClose" quando le persone sono a meno di tale distanza.|
| `maximum_distance_threshold` | float| Distanza in piedi che attiverà un evento "TooFar" quando le persone sono superiori a tale distanza.|
| `aggregation_method` | string| Metodo per il risultato di persondistance aggregato. Il aggregation_method è applicabile sia `mode` a che `average` a .|
| `focus` | string| Posizione del punto all'interno del rettangolo di selezione della persona usata per calcolare gli eventi. Il valore dello stato attivo può essere (footprint della persona), (il centro inferiore del rettangolo di selezione della persona), (il centro del rettangolo di `footprint` `bottom_center` selezione della `center` persona).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Configurazione per cognitiveservices.vision.spatialanalysis
Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una riga e una zona per **cognitiveservices.vision.spatialanalysis.** È possibile configurare più righe/zone per questa operazione e ogni riga/zona può avere eventi diversi.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Configurazione della fotocamera

Vedere le linee [guida sul posizionamento](spatial-analysis-camera-placement.md) della fotocamera per altre informazioni su come configurare zone e linee.

## <a name="spatial-analysis-operation-output"></a>Output dell'operazione di analisi spaziale

Gli eventi di ogni operazione vengono in uscita per hub IoT di Azure in formato JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formato JSON per cognitiveservices.vision.spatialanalysis-personcount Informazioni dettagliate sull'intelligenza artificiale

Json di esempio per l'output di un evento da questa operazione.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco del rilevamento della persona che ha attivato l'evento|
| `properties` | collection| Raccolta di valori|
| `trackinId` | string| Identificatore univoco della persona rilevata|
| `zone` | string | Campo "name" del poligono che rappresenta la zona attraversata|
| `trigger` | string| Il tipo di trigger è 'event' o 'interval' a seconda del valore `trigger` di in SPACEANALYTICS_CONFIG|

| Nome campo rilevamenti | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| Punti in alto a sinistra e in basso a destra quando il tipo di area è RECTANGLE |
| `confidence` | float| Attendibilità degli algoritmi|
| `face_mask` | float | Il valore di attendibilità dell'attributo con intervallo (0-1) indica che la persona rilevata sta indossando una maschera del viso |
| `face_nomask` | float | Il valore di attendibilità dell'attributo con intervallo (0-1) indica che la persona rilevata **non** sta indossando una maschera del viso |

| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | data| Data UTC in cui è stato generato il payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza dei fotogrammi video|
| `frameId` | INT | Identificatore del frame|
| `cameraCallibrationInfo` | collection | Raccolta di valori|
| `status` | string | Stato della calibrazione nel formato `state[;progress description]` . Lo stato può essere `Calibrating` , `Recalibrating` (se la ricalibrazione è abilitata) o `Calibrated` . La parte relativa alla descrizione dello stato è valida solo quando si trova nello stato e , che viene usata per visualizzare lo stato `Calibrating` `Recalibrating` del processo di calibrazione corrente.|
| `cameraHeight` | float | Altezza della fotocamera sopra il suolo in piedi. Ciò viene dedotto dalla calibrazione automatica. |
| `focalLength` | float | Lunghezza focale della fotocamera in pixel. Ciò viene dedotto dalla calibrazione automatica. |
| `tiltUpAngle` | float | Angolo di inclinazione della fotocamera da verticale. Ciò viene dedotto dalla calibrazione automatica.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formato JSON per cognitiveservices.vision.spatialanalysis-personcrossingline Informazioni dettagliate sull'intelligenza artificiale

Json di esempio per i rilevamenti restituiti da questa operazione.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco della persona che ha attivato questo evento|
| `properties` | collection| Raccolta di valori|
| `trackinId` | string| Identificatore univoco della persona rilevata|
| `status` | string| Direzione degli attraversamenti di linea, 'CrossLeft' o 'CrossRight'. La direzione si basa sull'immaginare di essere in posizione di partenza verso la "fine" della linea. CrossRight attraversa da sinistra a destra. CrossLeft attraversa da destra a sinistra.|
| `zone` | string | Campo "name" della riga incrociata|

| Nome campo rilevamenti | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| In alto a sinistra e in basso a destra quando il tipo di area è RECTANGLE |
| `confidence` | float| Attendibilità degli algoritmi|
| `face_mask` | float | Il valore di attendibilità dell'attributo con intervallo (0-1) indica che la persona rilevata indossa una maschera viso |
| `face_nomask` | float | Il valore di attendibilità dell'attributo con intervallo (0-1) indica che la persona rilevata **non** indossa una maschera viso |

| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | data| Data UTC in cui è stato generato il payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza dei fotogrammi video|
| `frameId` | INT | Identificatore del frame|


> [!IMPORTANT]
> Il modello di intelligenza artificiale rileva una persona indipendentemente dal fatto che la persona sia rivolta verso o fuori dalla fotocamera. Il modello di intelligenza artificiale non esegue il riconoscimento del viso e non genera informazioni biometriche. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formato JSON per cognitiveservices.vision.spatialanalysis-personcrossingpolygon Informazioni dettagliate sull'intelligenza artificiale

Json di esempio per i rilevamenti restituiti da questa operazione con `zonecrossing` tipo SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Json di esempio per i rilevamenti restituiti da questa operazione con `zonedwelltime` tipo SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento. Il valore può essere _personZoneDwellTimeEvent_ o _personZoneEnterExitEvent_|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco della persona che ha attivato questo evento|
| `properties` | collection| Raccolta di valori|
| `trackinId` | string| Identificatore univoco della persona rilevata|
| `status` | string| Direzione degli attraversamenti poligonali, ad esempio 'Enter' o 'Exit'|
| `side` | INT| Numero del lato del poligono incrociato dalla persona. Ogni lato è un bordo numerato tra i due vertici del poligono che rappresenta la zona. Il bordo tra i primi due vertici del poligono rappresenta il primo lato. 'Side' è vuoto quando l'evento non è associato a un lato specifico a causa dell'occlusione. Ad esempio, un'uscita si è verificata quando una persona scompare ma non è stata vista attraversare un lato della zona o quando una persona è apparsa nella zona, ma non è stata vista attraversare un lato.|
| `durationMs` | float | Numero di millisecondi che rappresentano il tempo impiegato dalla persona nella zona. Questo campo viene fornito quando il tipo di evento è _personZoneDwellTimeEvent_|
| `zone` | string | Campo "name" del poligono che rappresenta la zona attraversata|

| Nome campo rilevamenti | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| In alto a sinistra e in basso a destra quando il tipo di area è RECTANGLE |
| `confidence` | float| Attendibilità degli algoritmi|
| `face_mask` | float | Il valore di attendibilità dell'attributo con intervallo (0-1) indica che la persona rilevata indossa una maschera viso |
| `face_nomask` | float | Il valore di attendibilità dell'attributo con intervallo (0-1) indica che la persona rilevata **non** indossa una maschera viso |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formato JSON per cognitiveservices.vision.spatialanalysis-persondistance Informazioni dettagliate sull'intelligenza artificiale

Json di esempio per i rilevamenti restituiti da questa operazione.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco della persona che ha attivato questo evento|
| `properties` | collection| Raccolta di valori|
| `personCount` | INT| Numero di persone rilevate quando è stato generato l'evento|
| `averageDistance` | float| Distanza media tra tutte le persone rilevate in piedi|
| `minimumDistanceThreshold` | float| Distanza in piedi che attiverà un evento "TooClose" quando le persone sono a una distanza inferiore a quella.|
| `maximumDistanceThreshold` | float| Distanza in piedi che attiverà un evento "TooFar" quando le persone sono più distanti.|
| `eventName` | string| Il nome `TooClose` `minimumDistanceThreshold` dell'evento è con viene violato, quando viene `TooFar` `maximumDistanceThreshold` violato o quando la `unknown` calibrazione automatica non è stata completata|
| `distanceViolationPersonCount` | INT| Numero di persone rilevate in violazione `minimumDistanceThreshold` di o `maximumDistanceThreshold`|
| `zone` | string | Campo "name" del poligono che rappresenta la zona monitorata per la distanziazione tra persone|
| `trigger` | string| Il tipo di trigger è 'event' o 'interval' a seconda del valore `trigger` di in SPACEANALYTICS_CONFIG|

| Nome campo rilevamenti | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| In alto a sinistra e in basso a destra quando il tipo di area è RECTANGLE |
| `confidence` | float| Attendibilità degli algoritmi|
| `centerGroundPoint` | 2 valori float| `x`, valori con le coordinate della posizione dedosata della persona `y` sul terreno in piedi. `x` e `y` sono coordinate sul piano terra, presupponendo che il piano sia a livello. La posizione della fotocamera è l'origine. |

Quando si calcola , è la distanza dalla fotocamera alla persona lungo `centerGroundPoint` `x` una linea perpendicolare al piano immagine della fotocamera. `y` è la distanza dalla fotocamera alla persona lungo una linea parallela al piano immagine della fotocamera. 

![Esempio di punto di terra centrale](./media/spatial-analysis/x-y-chart.png) 

In this example, l'evento `centerGroundPoint` è `{x: 4, y: 5}`. Ciò significa che c'è una persona a 4 metri di distanza dalla fotocamera e 5 piedi a destra, che guarda la stanza dall'alto verso il basso.


| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | data| Data UTC in cui è stato generato il payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza dei fotogrammi video|
| `frameId` | INT | Identificatore del frame|
| `cameraCallibrationInfo` | collection | Raccolta di valori|
| `status` | string | Stato della calibrazione nel formato `state[;progress description]` . Lo stato può essere `Calibrating` , `Recalibrating` (se la ricalibrazione è abilitata) o `Calibrated` . La parte relativa alla descrizione dello stato di avanzamento è valida solo quando è in stato e , usata per mostrare lo stato di avanzamento `Calibrating` `Recalibrating` del processo di calibrazione corrente.|
| `cameraHeight` | float | Altezza della fotocamera sopra il suolo in piedi. Ciò viene dedotto dalla calibrazione automatica. |
| `focalLength` | float | Lunghezza focale della fotocamera in pixel. Ciò viene dedotto dalla calibrazione automatica. |
| `tiltUpAngle` | float | Angolo di inclinazione della fotocamera da verticale. Ciò viene dedotto dalla calibrazione automatica.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>Formato JSON per cognitiveservices.vision.spatialanalysis Informazioni dettagliate sull'intelligenza artificiale

L'output di questa operazione dipende dalla configurazione di , ad esempio se è presente un evento configurato `events` `zonecrossing` per questa operazione, l'output sarà uguale a `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>Usare l'output generato dal contenitore

È possibile integrare il rilevamento o gli eventi di analisi spaziale nell'applicazione. Ecco alcuni approcci da considerare: 

* Usare Azure Event Hub SDK per il linguaggio di programmazione scelto per connettersi all'endpoint hub IoT di Azure e ricevere gli eventi. Per altre informazioni, vedere Leggere messaggi da dispositivo a [cloud dall'endpoint](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) predefinito. 
* Configurare il **routing dei** messaggi nel hub IoT di Azure per inviare gli eventi ad altri endpoint o salvare gli eventi nell'archivio dati. Per altre [informazioni, vedere Routing dei messaggi dell'hub IoT.](../../iot-hub/iot-hub-devguide-messages-d2c.md) 
* Configurare un Analisi di flusso di Azure processo per elaborare gli eventi in tempo reale non appena arrivano e creare visualizzazioni. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Distribuzione di operazioni di analisi spaziale su larga scala (più fotocamere)

Per ottenere prestazioni e utilizzo ottimali delle GPU, è possibile distribuire qualsiasi operazione di analisi spaziale su più fotocamere usando istanze del grafo. Di seguito è riportato un esempio per l'esecuzione `cognitiveservices.vision.spatialanalysis-personcrossingline` dell'operazione su quindici fotocamere.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `batch_size` | INT | Se tutte le fotocamere hanno la stessa risoluzione, impostare sul numero di fotocamere che verranno usate nell'operazione; in caso contrario, impostare su 1 o lasciare il valore predefinito `batch_size` (1), che indica che non è supportato alcun `batch_size` batch. |

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web People Counting](spatial-analysis-web-app.md)
* [Registrazione e risoluzione dei problemi](spatial-analysis-logging.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)
