---
title: Monitoraggio e registrazione-Azure
description: Questo articolo fornisce una panoramica del monitoraggio e della registrazione di analisi video in tempo reale su IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 08b2f5cce80581d71ce73e97ab30900aa8957c77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564484"
---
# <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

In questo articolo si apprenderà come ricevere gli eventi per il monitoraggio remoto dal modulo di analisi video in tempo reale su IoT Edge. 

Si apprenderà anche come controllare i log generati dal modulo.

## <a name="taxonomy-of-events"></a>Tassonomia degli eventi

Analisi video in tempo reale su IoT Edge emette eventi o dati di telemetria in base alla tassonomia seguente:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagramma che mostra la tassonomia degli eventi.":::

* Operativo: eventi generati dalle azioni di un utente o durante l'esecuzione di un [grafico multimediale](media-graph-concept.md)
   
   * Volume: prevista bassa (alcune volte al minuto o addirittura inferiore)
   * Esempi:

      - Registrazione avviata (mostrata nell'esempio seguente)
      - Registrazione arrestata
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostica: eventi che consentono di diagnosticare i problemi relativi alle prestazioni

   * Volume: può essere alto (più volte al minuto)
   * Esempi:
   
      - Informazioni su RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) (mostrate nell'esempio seguente) 
      - Gap nel feed video in arrivo

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: eventi generati come parte dell'analisi video

   * Volume: può essere alto (più volte al minuto o più)
   * Esempi:
      
      - Movimento rilevato (illustrato nell'esempio seguente) 
      - Risultato dell'inferenza

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Gli eventi generati dal modulo vengono inviati all' [hub IOT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). Possono essere indirizzati da lì ad altre destinazioni. 

### <a name="timestamps-in-analytic-events"></a>Timestamp negli eventi analitici

Come indicato in precedenza, gli eventi generati come parte dell'analisi video hanno timestamp associati. Se è stato [registrato il video live](video-recording-concept.md) come parte della topologia Graph, questi timestamp consentono di individuare la posizione del video registrato in cui si è verificato l'evento specifico. Di seguito sono riportate le linee guida su come eseguire il mapping del timestamp in un evento analitico alla sequenza temporale del video registrato in un [asset di servizi multimediali di Azure](terminology.md#asset).

Estrarre innanzitutto il `eventTime` valore. Usare questo valore in un [filtro di intervallo di tempo](playback-recordings-how-to.md#time-range-filters) per recuperare una parte appropriata della registrazione. Ad esempio, potrebbe essere necessario recuperare un video che inizia 30 secondi prima `eventTime` e termina 30 secondi. Per l'esempio precedente, dove `eventTime` è 2020-05-12T23:33:09.381 z, una richiesta di un manifesto HLS per 30 secondi prima e dopo `eventTime` avrà un aspetto simile a questa richiesta:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

L'URL precedente restituisce una [playlist Master](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) che contiene gli URL per le playlist multimediali. La playlist multimediale contiene voci come questa:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
La voce precedente segnala che è disponibile un frammento video che inizia in corrispondenza `timestamp` di un valore di `143039375031270` . Il `timestamp` valore nell'evento analitico usa la stessa scala cronologica della playlist multimediale. Può essere usato per identificare il frammento video pertinente e cercare il frame corretto.

Per altre informazioni, vedere questi [articoli sulla ricerca con frame-accurate](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) in HLS.

## <a name="controlling-events"></a>Controllo degli eventi

È possibile usare le proprietà del dispositivo gemello seguenti per controllare gli eventi operativi e diagnostici pubblicati dal modulo di analisi video live in IoT Edge. Queste proprietà sono documentate nello [schema JSON del modulo gemello](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName`: Per ottenere gli eventi di diagnostica dal modulo, includere questa proprietà e fornire qualsiasi valore. Ometterlo o lasciarlo vuoto per arrestare la pubblicazione di eventi di diagnostica da parte del modulo.
   
- `operationalEventsOutputName`: Per ottenere gli eventi operativi dal modulo, includere questa proprietà e fornire qualsiasi valore. Ometterlo o lasciarlo vuoto per arrestare la pubblicazione di eventi operativi da parte del modulo.
   
Gli eventi di analisi vengono generati da nodi come il processore di rilevamento del movimento o il processore di estensione HTTP. Il sink dell'hub Internet viene usato per inviarli all'hub IoT Edge. 

È possibile controllare il [routing di tutti gli eventi precedenti](../../iot-edge/module-composition.md#declare-routes) usando la `desired` proprietà del `$edgeHub` modulo gemello nel manifesto di distribuzione:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Nel codice JSON precedente `lvaEdge` è il nome dell'analisi video live sul modulo IOT Edge. La regola di routing segue lo schema definito in [Declare Routes](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Per assicurarsi che gli eventi di analisi raggiungano l'hub di IoT Edge, è necessario disporre di un nodo del sink dell'hub Internet a valle di qualsiasi nodo del processore di rilevamento del movimento e/o di qualsiasi nodo del processore di estensione HTTP.

## <a name="event-schema"></a>Schema di eventi

Gli eventi hanno origine nel dispositivo perimetrale e possono essere usati a bordo o nel cloud. Gli eventi generati da analisi video in tempo reale su IoT Edge sono conformi al [modello di messaggistica di streaming](../../iot-hub/iot-hub-devguide-messages-construct.md) stabilito dall'hub Azure. Il modello è costituito da proprietà di sistema, proprietà dell'applicazione e corpo.

### <a name="summary"></a>Riepilogo

Ogni evento, se osservato tramite l'hub Internet, ha un set di proprietà comuni:

|Proprietà   |Tipo di proprietà| Tipo di dati   |Descrizione|
|---|---|---|---|
|`message-id`   |sistema |guid|  ID evento univoco.|
|`topic`|   applicationProperty |string|    Percorso Azure Resource Manager per l'account servizi multimediali di Azure.|
|`subject`| applicationProperty |string|    Sottopercorso dell'entità che emette l'evento.|
|`eventTime`|   applicationProperty|    string| Ora di generazione dell'evento.|
|`eventType`|   applicationProperty |string|    Identificatore del tipo di evento. Vedere la sezione seguente.|
|`body`|Corpo    |object|    Particolari dati dell'evento.|
|`dataVersion`  |applicationProperty|   string  |{Major}. Secondaria|

### <a name="properties"></a>Proprietà

#### <a name="message-id"></a>message-id

Identificatore univoco globale (GUID) per l'evento.

#### <a name="topic"></a>argomento

Rappresenta l'account di servizi multimediali di Azure associato al grafo.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Entità che emette l'evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

La `subject` proprietà consente di eseguire il mapping degli eventi generici al modulo di generazione. Per un nome utente o una password RTSP non valida, ad esempio, l'evento generato si trova `Microsoft.Media.Graph.Diagnostics.ProtocolError` nel `/graphInstances/myGraph/sources/myRtspSource` nodo.

#### <a name="event-types"></a>Tipi di eventi

I tipi di evento vengono assegnati a uno spazio dei nomi in base a questo schema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Classi di evento

|Nome di classe|Descrizione|
|---|---|
|Analisi  |Eventi generati come parte dell'analisi del contenuto.|
|Diagnostica    |Eventi che contribuiscono alla diagnostica dei problemi e delle prestazioni.|
|Operativo    |Eventi generati come parte dell'operazione di risorsa.|

I tipi di evento sono specifici per ogni classe di evento.

Esempi:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Ora dell'evento

L'ora dell'evento è formattata in una stringa ISO 8601. Rappresenta l'ora in cui si è verificato l'evento.

### <a name="azure-monitor-collection-via-telegraf"></a>Raccolta di monitoraggio di Azure tramite Telegraf

Queste metriche verranno segnalate dall'analisi video live sul modulo IoT Edge:  

|Nome metrica|Tipo|Etichetta|Descrizione|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Misuratore|iothub, edge_device, module_name, graph_topology|Numero totale di grafici attivi per topologia.|
|lva_received_bytes_total|Contatore|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Numero totale di byte ricevuti da un nodo. Supportato solo per le origini RTSP.|
|lva_data_dropped_total|Contatore|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node data_kind|Contatore di tutti i dati eliminati (eventi, supporti e così via).|

> [!NOTE]
> Un [endpoint Prometheus](https://prometheus.io/docs/practices/naming/) viene esposto sulla porta 9600 del contenitore. Se si assegna un nome all'analisi dei video live sul modulo IoT Edge "lvaEdge", sarà possibile accedere alle metriche inviando una richiesta GET a http://lvaEdge:9600/metrics .   

Seguire questa procedura per abilitare la raccolta di metriche dall'analisi video live sul modulo IoT Edge:

1. Creare una cartella nel computer di sviluppo e passare a tale cartella.

1. Nella cartella creare un file contenente `telegraf.toml` le configurazioni seguenti:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Assicurarsi di sostituire le variabili nel file con estensione toml. Le variabili sono identificate da parentesi graffe ( `{}` ).

1. Nella stessa cartella creare un Dockerfile che contenga i comandi seguenti:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Usare i comandi dell'interfaccia della riga di comando di Docker per compilare il file Docker e pubblicare l'immagine nel registro contenitori di Azure.
    
   Per altre informazioni sull'uso dell'interfaccia della riga di comando di Docker per eseguire il push in un registro contenitori, vedere [push e pull di immagini Docker](../../container-registry/container-registry-get-started-docker-cli.md). Per altre informazioni su Azure Container Registry, vedere la [documentazione](../../container-registry/index.yml).


1. Al termine del push in Azure Container Registry, aggiungere il nodo seguente al file manifesto di distribuzione:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Assicurarsi di sostituire le variabili nel file manifesto. Le variabili sono identificate da parentesi graffe ( `{}` ).


   Monitoraggio di Azure può essere [autenticato tramite l'entità servizio](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Il plug-in di monitoraggio di Azure per Telegraf espone [diversi metodi di autenticazione](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Per usare l'autenticazione basata su entità servizio, impostare le variabili di ambiente seguenti:  
     `AZURE_TENANT_ID`: Specifica il tenant in cui eseguire l'autenticazione.  
     `AZURE_CLIENT_ID`: Specifica l'ID client dell'app da usare.  
     `AZURE_CLIENT_SECRET`: Specifica il segreto dell'app da usare.  
     
     >[!TIP]
     > È possibile assegnare all'entità servizio il ruolo **server di pubblicazione metriche di monitoraggio** . Eseguire la procedura descritta in **[creare un'entità servizio](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** per creare l'entità servizio e assegnare il ruolo.

1. Una volta distribuiti i moduli, le metriche verranno visualizzate in monitoraggio di Azure in un singolo spazio dei nomi. I nomi delle metriche corrisponderanno a quelli emessi da Prometheus. 

   In questo caso, nella portale di Azure passare all'hub Internet e selezionare **metriche** nel riquadro sinistro. Verranno visualizzate le metriche.

### <a name="log-analytics-metrics-collection"></a>Raccolta di metriche Log Analytics
Usando l' [endpoint Prometheus](https://prometheus.io/docs/practices/naming/) insieme a [log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md), è possibile generare e [monitorare le metriche](../../azure-monitor/essentials/metrics-supported.md) , ad esempio CPUPercent, MemoryUsedPercent e così via.   

> [!NOTE]
> La configurazione seguente non raccoglie i log, **ma solo le metriche**. È possibile estendere il modulo dell'agente di raccolta per raccogliere e caricare anche i log.

[![Diagramma che mostra la raccolta di metriche con log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Informazioni su come [raccogliere le metriche](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Usare i comandi dell'interfaccia della riga di comando di Docker per compilare il [file Docker](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) e pubblicare l'immagine nel registro contenitori di Azure.
    
   Per altre informazioni sull'uso dell'interfaccia della riga di comando di Docker per eseguire il push in un registro contenitori, vedere [push e pull di immagini Docker](../../container-registry/container-registry-get-started-docker-cli.md). Per altre informazioni su Azure Container Registry, vedere la [documentazione](../../container-registry/index.yml).

1. Una volta completato il push in Azure Container Registry, nel manifesto di distribuzione viene inserito il codice seguente:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > I moduli `edgeHub` `edgeAgent` e `lvaEdge` sono i nomi dei moduli definiti nel file manifesto di distribuzione. Verificare che i nomi dei moduli corrispondano.   

    È possibile ottenere i `LogAnalyticsWorkspaceId` `LogAnalyticsSharedKey` valori e attenendosi alla procedura seguente:
    1. Accedere al portale di Azure
    1. Cerca le aree di lavoro Log Analytics
    1. Una volta individuata l'area di lavoro Log Analytics, passare all' `Agents management` opzione nel riquadro di spostamento a sinistra.
    1. Sono disponibili l'ID dell'area di lavoro e le chiavi segrete che è possibile usare.

1. Successivamente, creare una cartella di lavoro facendo clic sulla `Workbooks` scheda nel riquadro di spostamento a sinistra.
1. Usando il linguaggio di query kusto, è possibile scrivere query come riportato di seguito e ottenere la percentuale di CPU usata dai moduli IoT Edge.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagramma che mostra le metriche usando la query kusto.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Registrazione

Come per gli altri moduli IoT Edge, è anche possibile [esaminare i log del contenitore](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) nel dispositivo perimetrale. È possibile configurare le informazioni scritte nei log usando le proprietà [gemelle del modulo seguenti](module-twin-configuration-schema.md) :

* `logLevel`

   * I valori consentiti sono `Verbose`, `Information`, `Warning`, `Error` e `None`.
   * Il valore predefinito è `Information`. I registri contengono messaggi di errore, di avviso e informativi.
   * Se si imposta il valore su `Warning` , i log conterranno i messaggi di errore e di avviso.
   * Se si imposta il valore su `Error` , i log conterranno solo messaggi di errore.
   * Se si imposta il valore su `None` , non verrà generato alcun log. Questa configurazione non è consigliata.
   * Usare `Verbose` solo se è necessario condividere i log con il supporto di Azure per diagnosticare un problema.

* `logCategories`

   * Elenco delimitato da virgole di uno o più dei valori seguenti: `Application` , `Events` , `MediaPipeline` .
   * Il valore predefinito è `Application, Events`.
   * `Application`: Informazioni generali del modulo, ad esempio messaggi di avvio del modulo, errori di ambiente e chiamate a metodi diretti.
   * `Events`: Tutti gli eventi descritti in precedenza in questo articolo.
   * `MediaPipeline`: Log di basso livello che potrebbero offrire informazioni utili per la risoluzione dei problemi, ad esempio le difficoltà che stabiliscono una connessione con una fotocamera che supporta RTSP.
   
### <a name="generating-debug-logs"></a>Generazione dei log di debug

In alcuni casi, per consentire al supporto tecnico di Azure di risolvere un problema, potrebbe essere necessario generare log più dettagliati rispetto a quelli descritti in precedenza. Per generare i log seguenti:

1. [Collegare lo spazio di archiviazione del modulo alla risorsa di archiviazione del dispositivo](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) tramite `createOptions` . Se si esamina un [modello di manifesto di distribuzione](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) dalle guide introduttive, verrà visualizzato il codice seguente:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Questo codice consente al modulo perimetrale di scrivere i log nel percorso di archiviazione del dispositivo `/var/local/mediaservices/` . 

 1. Aggiungere la `desired` proprietà seguente al modulo:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Il modulo ora scriverà i log di debug in formato binario nel percorso di archiviazione del dispositivo `/var/local/mediaservices/debuglogs/` . È possibile condividere questi log con il supporto di Azure.

## <a name="faq"></a>Domande frequenti

Per domande, vedere le domande [frequenti su monitoraggio e metriche](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Passaggi successivi

[Registrazione continua di video](continuous-video-recording-tutorial.md)