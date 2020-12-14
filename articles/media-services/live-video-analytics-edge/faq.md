---
title: Analisi di video in tempo reale su IoT Edge domande frequenti-Azure
description: Questo argomento fornisce le risposte alle analisi video in tempo reale su IoT Edge domande frequenti.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401577"
---
# <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

Questo argomento fornisce le risposte alle analisi video in tempo reale su IoT Edge domande frequenti.

## <a name="general"></a>Generale

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Quali sono le variabili di sistema che è possibile usare nella definizione della topologia Graph?

|Variabile   |Descrizione|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Rappresenta un istante nell'ora UTC, espresso in genere come data e ora del giorno (rappresentazione di base yyyyMMddTHHmmssZ).|
|System. PreciseDateTime|Rappresenta un'istanza di data e ora UTC nel formato conforme al file ISO8601 con millisecondi (rappresentazione di base AAAAMMGGThhmmss. fffZ).|
|System. GraphTopologyName   |Rappresenta una topologia del grafico multimediale, che include il progetto di un grafico.|
|System. GraphInstanceName|  Rappresenta un'istanza del grafico multimediale, include i valori dei parametri e fa riferimento alla topologia.|

## <a name="configuration-and-deployment"></a>Configurazione e distribuzione

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>È possibile distribuire il modulo Media Edge in un dispositivo Windows 10?

Sì. Vedere l'articolo sui [contenitori Linux in Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Acquisisci dalla fotocamera IP e dalle impostazioni RTSP

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>È necessario usare un SDK speciale sul dispositivo per inviare un flusso video?

No. Analisi video in tempo reale su IoT Edge supporta l'acquisizione di supporti tramite il protocollo di streaming video RTSP (supportato nella maggior parte delle fotocamere IP).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>È possibile eseguire il push di contenuti multimediali in analisi video in tempo reale su IoT Edge usando RTMP o Smooth (come un evento live di servizi multimediali)?

* No. Analisi video Live supporta solo RTSP per l'acquisizione di video da fotocamere IP.
* Qualsiasi fotocamera che supporta lo streaming RTSP su TCP/HTTP dovrebbe funzionare. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>È possibile reimpostare o aggiornare l'URL dell'origine RTSP in un'istanza del grafo?

Sì, quando l'istanza del grafo è in stato inattivo.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>È disponibile un simulatore RTSP da usare durante i test e lo sviluppo?

Sì. Per supportare il processo di apprendimento è disponibile un modulo [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge disponibile per l'uso nelle guide introduttive e nelle esercitazioni. Questo modulo viene fornito come miglior tentativo e potrebbe non essere sempre disponibile. Si consiglia vivamente di non usare questa operazione per più di alcune ore. È consigliabile investire nei test con l'origine RTSP effettiva prima di creare piani per una distribuzione di produzione.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>L'individuazione ONVIF di fotocamere IP in rete perimetrale è supportata?

No, non è previsto alcun supporto per l'individuazione ONVIF dei dispositivi perimetrali.

## <a name="streaming-and-playback"></a>Streaming e riproduzione

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Le risorse registrate in AMS dal dispositivo perimetrale possono essere riprodotte usando tecnologie di streaming di servizi multimediali come HLS o DASH?

Sì. Gli asset registrati possono essere trasmessi come qualsiasi altro asset in servizi multimediali di Azure. Per eseguire lo streaming del contenuto, è necessario che sia stato creato un endpoint di streaming e che si trovi nello stato in esecuzione. L'uso del processo di creazione del localizzatore di streaming standard consentirà di accedere a un manifesto HLS o DASH per lo streaming a qualsiasi framework di Player idoneo. Per informazioni dettagliate sulla creazione di manifesti HLS o DASH, vedere Creazione [dinamica dei pacchetti](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>È possibile usare le funzionalità di protezione del contenuto standard e DRM di servizi multimediali in un asset archiviato?

Sì. Tutte le funzionalità di protezione del contenuto della crittografia dinamica standard e DRM sono disponibili per l'uso negli asset registrati da un grafico multimediale.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Quali giocatori è possibile usare per visualizzare il contenuto degli asset registrati?

Sono supportati tutti i lettori standard che supportano Apple HTTP Live Streaming (HLS) versione 3 o 4. Inoltre, è supportato anche qualsiasi lettore in grado di riprodurre MPEG-DASH compatibile.

I giocatori consigliati per i test includono:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Lettore Shaka](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [HTTP Live Streaming nativo Apple](https://developer.apple.com/streaming/)
* Microsoft Edge, Chrome o Safari incorporato nel lettore video HTML5
* Lettori commerciali che supportano la riproduzione HLS o DASH

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Quali sono i limiti per lo streaming di un asset del grafico multimediale?

Il flusso di un asset attivo o registrato da un grafico multimediale usa lo stesso endpoint di streaming e infrastruttura a scalabilità elevata supportato da servizi multimediali per streaming on demand e Live per i clienti di media & Entertainment, OTT e broadcast. Ciò significa che è possibile abilitare in modo rapido e semplice la rete CDN di Azure, Verizon o Akamai per distribuire il contenuto a un pubblico con un numero ridotto di utenti o fino a milioni a seconda dello scenario.

Il contenuto può essere recapitato usando Apple HTTP Live Streaming (HLS) o MPEG-DASH.

## <a name="design-your-ai-model"></a>Progettare il modello di intelligenza artificiale 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Sono disponibili più modelli di intelligenza artificiale in un contenitore docker. Come è possibile usarli con analisi video in tempo reale? 

Le soluzioni sono diverse a seconda del protocollo di comunicazione utilizzato dal server di inferenza per comunicare con analisi video in tempo reale. Di seguito sono riportati alcuni modi per eseguire questa operazione.

#### <a name="http-protocol"></a>Protocollo HTTP:

* Singolo contenitore (lvaExtension singolo):  

   Nel server di inferenza è possibile usare una singola porta ma endpoint diversi per i diversi modelli di intelligenza artificiale. Per un esempio di Python, ad esempio, è possibile usare diversi `route` per modello come: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Quindi, nella distribuzione di analisi video in tempo reale, quando si creano istanze dei grafici, impostare l'URL del server di inferenza per ogni istanza come: 

   prima istanza: URL del server di inferenza =`http://lvaExtension:44000/score/face_detection`<br/>
   seconda istanza: URL del server di inferenza =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > In alternativa, è anche possibile esporre i modelli di intelligenza artificiale su porte diverse e chiamarli quando si creano istanze dei grafici.  

* Più contenitori: 

   Ogni contenitore viene distribuito con un nome diverso. Attualmente, nel set di documentazione di Live Video Analytics è stato illustrato come distribuire un'estensione con il nome: **lvaExtension**. A questo punto è possibile sviluppare due contenitori diversi. Ogni contenitore ha la stessa interfaccia HTTP (ovvero lo stesso `/score` endpoint). Distribuire questi due contenitori con nomi diversi e assicurarsi che entrambi siano in ascolto su **porte diverse**. 

   Ad esempio, un contenitore con il nome `lvaExtension1` è in ascolto della porta `44000` , mentre un altro contenitore con il nome `lvaExtension2` è in ascolto della porta `44001` . 

   Nella topologia di analisi video live si crea un'istanza di due grafici con URL di inferenza diversi, ad esempio: 

   Prima istanza: URL del server di inferenza = `http://lvaExtension1:44001/score`    
   Seconda istanza: URL del server di inferenza = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>Protocollo GRPC: 

Con il modulo di analisi video Live 1,0, quando si usa un protocollo gRPC, l'unico modo possibile è se il server gRPC ha esposto diversi modelli di intelligenza artificiale tramite porte diverse. In [questo esempio](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)è presente una sola porta, 44000, che espone tutti i modelli Yolo. In teoria, è possibile riscrivere il server Yolo gRPC per esporre alcuni modelli alle 44000, altri a 45000... 

Con il modulo di analisi video Live 2,0, viene aggiunta una nuova proprietà al nodo dell'estensione gRPC. Questa proprietà è denominata **extensionConfiguration** , che è una stringa facoltativa che può essere utilizzata come parte del contratto gRPC. Quando si dispone di più modelli di intelligenza artificiale in un unico server di inferenza, non sarà necessario esporre un nodo per ogni modello di intelligenza artificiale. Al contrario, per un'istanza di Graph, il provider di estensioni (l'utente) può definire come selezionare i diversi modelli di intelligenza artificiale usando la proprietà **extensionConfiguration** e durante l'esecuzione, analisi video in tempo reale passerà questa stringa al server di inferenza che può usare questo per richiamare il modello di intelligenza artificiale desiderato. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Sto creando un server gRPC per un modello di intelligenza artificiale e voglio essere in grado di supportare l'uso di più fotocamere/istanze di Graph. Come si compila il server? 

 Prima di tutto, assicurarsi che il server sia in grado di gestire più di una richiesta alla volta. Oppure verificare che il server funzioni nei thread paralleli. 

Ad esempio, in uno degli [esempi Live Video Analytics GRPC](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)è presente un numero predefinito di canali paralleli impostati. Vedere: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Nella creazione di un'istanza del server gRPC precedente, il server può aprire solo tre canali per videocamera (per ogni istanza di topologia Graph) alla volta. Non provare a connettere più di tre istanze al server. Se si tenta di aprire più di tre canali, le richieste saranno in sospeso fino a quando non ne viene eliminata una esistente.  

Sopra l'implementazione del server gRPC viene usato negli esempi di Python. Gli sviluppatori possono implementare i propri server o nell'implementazione predefinita precedente possono aumentare il numero di lavoro impostato sul numero di fotocamere usate per ottenere il feed video da. 

Per configurare e utilizzare più fotocamere, gli sviluppatori possono creare istanze di più istanze di topologia Graph in cui ogni istanza punta allo stesso server di inferenza o a un server di inferenza diverso (ad esempio, il server indicato nel paragrafo precedente). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Desidero essere in grado di ricevere più frame da upstream prima di prendere una decisione di inferenza. Come è possibile abilitarla? 

Gli [esempi predefiniti](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) correnti funzionano in modalità "senza stato". In questo esempio non viene mantenuto lo stato delle chiamate precedenti e anche chi ha chiamato (ovvero l'istanza di più topologia può chiamare lo stesso server di inferenza e il server non sarà in grado di distinguere chi chiama e lo stato per ogni chiamante) 

#### <a name="http-protocol"></a>Protocollo HTTP

Quando si usa il protocollo HTTP: 

Per Mantenete lo stato, ogni chiamante (istanza della topologia Graph) chiamerà il server di inferenza con il parametro di query HTTP univoco per il chiamante. Ad esempio, l'indirizzo URL del server di inferenza per  

1 istanza topologia = `http://lvaExtension:44000/score?id=1`<br/>
seconda istanza di topologia = `http://lvaExtension:44000/score?id=2`

… 

Sul lato server, la route del Punteggio saprà chi sta chiamando. Se ID = 1, può mantenerne lo stato separatamente per il chiamante (istanza della topologia Graph). È quindi possibile lasciare i frame video ricevuti in un buffer, ad esempio una matrice o un dizionario con chiave DateTime, e il valore è il frame, quindi è possibile definire il server da elaborare (dedurre) dopo la ricezione dei frame x. 

#### <a name="grpc-protocol"></a>Protocollo GRPC 

Quando si usa il protocollo gRPC: 

Con un'estensione gRPC, ogni sessione è destinata a un singolo feed della fotocamera, pertanto non è necessario specificare un ID. Ora, con la proprietà extensionConfiguration, è possibile archiviare i fotogrammi video in un buffer e definire il server da elaborare (dedurre) dopo la ricezione di x frame. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Tutti i ProcessMediaStreams di un determinato contenitore eseguono lo stesso modello di intelligenza artificiale? 

No.  

Le chiamate di avvio/arresto dall'utente finale in un'istanza di Graph costituiscono una sessione o forse è presente una disconnessione/riconnessione della fotocamera. L'obiettivo è quello di salvare in modo permanente una sessione se la fotocamera esegue lo streaming di video. 

* Due fotocamere che inviano video per l'elaborazione, creano due sessioni. 
* Una fotocamera che passa a un grafico con due nodi gRPCExtension crea due sessioni. 

Ogni sessione è una connessione duplex completa tra analisi video in tempo reale e il server gRPC e ogni sessione può avere un modello/pipeline diverso. 

> [!NOTE]
> In caso di disconnessione/riconnessione della fotocamera (la fotocamera passa alla modalità offline per un periodo oltre i limiti di tolleranza), analisi video in tempo reale apre una nuova sessione con il server gRPC. Non è necessario che il server tenga traccia dello stato tra le sessioni. 

Analisi video in tempo reale ha aggiunto anche il supporto di più estensioni gRPC per una singola fotocamera in un'istanza di Graph. Sarà possibile usare queste estensioni gRPC per eseguire l'elaborazione di intelligenza artificiale in sequenza o in parallelo o anche avere una combinazione di entrambi. 

> [!NOTE]
> L'esecuzione in parallelo di più estensioni influirà sulle risorse hardware ed è necessario tenerne conto quando si sceglie l'hardware adatto alle proprie esigenze di calcolo. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Qual è il numero massimo di ProcessMediaStreams simultanei? 

Non esiste alcun limite a cui si applica analisi video in tempo reale.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Come decidere se il server di inferenza deve usare CPU o GPU o qualsiasi altro acceleratore hardware? 

Questo dipende completamente dal modo in cui viene sviluppato il modello di intelligenza artificiale e dal modo in cui lo sviluppatore vuole usare gli acceleratori della CPU e dell'hardware. Durante lo sviluppo del modello di intelligenza artificiale, gli sviluppatori possono specificare quali risorse devono essere utilizzate dal modello per eseguire le azioni. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Ricerca per categorie archiviare immagini con i rettangoli di delimitazione post-elaborazione? 

Attualmente, le coordinate del riquadro vengono fornite come solo messaggi di inferenza. Gli sviluppatori possono creare un flusso di trasmissione MJPEG personalizzato che può usare questi messaggi e sovrapporre i riquadri ai frame dei video.  

## <a name="grpc-compatibility"></a>compatibilità con gRPC 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Come è possibile sapere quali sono i campi obbligatori per il descrittore del flusso multimediale? 

A qualsiasi valore di campo a cui non viene fornito verrà assegnato un valore predefinito [come specificato da gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Analisi video Live usa la versione **proto3** della lingua del buffer del protocollo. Tutti i dati del buffer del protocollo usati dai contratti di analisi video live sono disponibili nei file del buffer del protocollo [definiti qui](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Come è possibile verificare che vengano utilizzati i file del buffer di protocollo più recenti? 

Qui è possibile [ottenere](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)i file del buffer del protocollo più recenti. Ogni volta che si aggiornano i file di contratto, verranno visualizzati in questa posizione. Sebbene non esista alcun piano immediato per aggiornare i file di protocollo, cercare il nome del pacchetto nella parte superiore dei file per conoscerne la versione. L'indicazione corretta è la seguente: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Eventuali aggiornamenti a questi file incrementeranno il valore "v-value" alla fine del nome. 

> [!NOTE]
> Poiché analisi video Live usa la versione proto3 del linguaggio, i campi sono facoltativi e questo lo rende compatibile con le versioni precedenti e successive. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Quali funzionalità di gRPC sono disponibili per l'uso con analisi video in tempo reale? Quali sono le funzionalità obbligatorie e quali sono facoltative? 

È possibile usare tutte le funzionalità di gRPC sul lato server purché il contratto protobuf sia stato completato. 

## <a name="monitoring-and-metrics"></a>Monitoraggio e metriche

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>È possibile monitorare il grafico multimediale sul perimetro usando griglia di eventi?

Sì. È possibile utilizzare la metrica Prometheus e pubblicarli in griglia di eventi. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>È possibile usare monitoraggio di Azure per visualizzare l'integrità, le metriche e le prestazioni dei grafici multimediali nel cloud o sul perimetro?

Sì. Questa configurazione è supportata. Altre informazioni su [come usare le metriche di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Sono disponibili strumenti che consentono di monitorare più facilmente il modulo IoT Edge di servizi multimediali?

Visual Studio Code supporta l'estensione "Azure Internet per gli strumenti", che consente di monitorare facilmente gli endpoint del modulo LVAEdge. È possibile usare questo strumento per avviare rapidamente il monitoraggio dell'endpoint predefinito dell'hub Internet per gli "eventi" e visualizzare i messaggi di inferenza indirizzati dal dispositivo perimetrale al cloud. 

Inoltre, è possibile usare questa estensione per modificare il modulo gemello per il modulo LVAEdge per modificare le impostazioni del grafico multimediale.

Per ulteriori informazioni, vedere l'articolo [monitoraggio e registrazione](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fatturazione e disponibilità

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Come viene fatturato l'analisi video in tempo reale IoT Edge?

Per informazioni dettagliate, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido: Introduzione: analisi di video live in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
