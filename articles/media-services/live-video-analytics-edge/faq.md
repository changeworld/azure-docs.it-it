---
title: Domande frequenti sull'analisi dei video in tempo reale su IoT Edge-Azure
description: Questo articolo risponde alle domande frequenti sull'analisi dei video in tempo reale su IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: f9ef26b9b64bd8a0bad7c83960f2d235ed6461cb
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762878"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Domande frequenti sull'analisi dei video in tempo reale su IoT Edge

Questo articolo risponde alle domande frequenti sull'analisi dei video in tempo reale su Azure IoT Edge.

## <a name="general"></a>Generale

**Quali variabili di sistema è possibile usare nella definizione della topologia Graph?**

| Variabile   |  Descrizione  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Rappresenta un istante nell'ora UTC, espresso in genere come data e ora del giorno nel formato seguente:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Rappresenta un'istanza di data e ora UTC (Coordinated Universal Time) in un formato conforme a file ISO8601 con millisecondi, nel formato seguente:<br>*AAAAMMGGThhmmss. fffZ* | 
| System. GraphTopologyName   | Rappresenta una topologia del grafico multimediale e include il progetto di un grafico. | 
| System. GraphInstanceName |    Rappresenta un'istanza del grafico multimediale, include i valori dei parametri e fa riferimento alla topologia. | 

## <a name="configuration-and-deployment"></a>Configurazione e distribuzione

**È possibile distribuire il modulo Media Edge in un dispositivo Windows 10?**

Sì. Per ulteriori informazioni, vedere la pagina [relativa ai contenitori Linux in Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Acquisisci dalla fotocamera IP e dalle impostazioni RTSP

**È necessario usare un SDK speciale sul dispositivo per inviare un flusso video?**

No, analisi video in tempo reale su IoT Edge supporta l'acquisizione di supporti usando RTSP (protocollo di streaming in tempo reale) per lo streaming video, supportato nella maggior parte delle fotocamere IP.

**È possibile eseguire il push di contenuti multimediali in analisi video in tempo reale su IoT Edge usando il protocollo RTMP (Real-Time Messaging Protocol) o il protocollo Smooth Streaming (ad esempio un evento live di servizi multimediali)?**

No, analisi video Live supporta solo RTSP per l'acquisizione di video da fotocamere IP. Qualsiasi fotocamera che supporta lo streaming RTSP su TCP/HTTP dovrebbe funzionare. 

**È possibile reimpostare o aggiornare l'URL di origine RTSP in un'istanza Graph?**

Sì, quando l'istanza del grafo è in stato *inattivo* .  

**È disponibile un simulatore RTSP da usare durante i test e lo sviluppo?**

Sì, un modulo [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge è disponibile per l'uso nelle guide introduttive ed esercitazioni per supportare il processo di apprendimento. Questo modulo viene fornito come massimo sforzo e potrebbe non essere sempre disponibile. Si consiglia vivamente di *non* usare il simulatore per più di alcune ore. È consigliabile investire nei test con l'origine RTSP effettiva prima di pianificare una distribuzione di produzione.

**L'individuazione ONVIF di fotocamere IP in rete perimetrale è supportata?**

No, non è supportata l'individuazione di dispositivi ONVIF (Open Network Video Interface Forum) sui dispositivi perimetrali.

## <a name="streaming-and-playback"></a>Streaming e riproduzione

**È possibile riprodurre risorse registrate in servizi multimediali di Azure dal perimetro usando tecnologie di streaming come HLS o DASH?**

Sì. È possibile trasmettere in streaming risorse registrate come qualsiasi altro asset in servizi multimediali di Azure. Per eseguire lo streaming del contenuto, è necessario che sia stato creato un endpoint di streaming e che si trovi nello stato in esecuzione. L'uso del processo di creazione del localizzatore di streaming standard ti permette di accedere a un manifesto Apple HTTP Live Streaming (HLS) o Dynamic Adaptive Streaming tramite HTTP (DASH, noto anche come MPEG-DASH) per lo streaming a qualsiasi framework di lettori idoneo. Per altre informazioni sulla creazione e sulla pubblicazione di manifesti HLS o DASH, vedere Creazione [dinamica dei pacchetti](../latest/dynamic-packaging-overview.md).

**È possibile usare le funzionalità di protezione del contenuto standard e DRM di servizi multimediali in un asset archiviato?**

Sì. Tutte le funzionalità di protezione del contenuto della crittografia dinamica standard e di Digital Rights Management (DRM) sono disponibili per l'uso in asset registrati da un grafico multimediale.

**Quali giocatori è possibile usare per visualizzare il contenuto degli asset registrati?**

Sono supportati tutti i lettori standard che supportano HLS compatibile con la versione 3 o 4. Inoltre, è supportato anche qualsiasi lettore in grado di riprodurre MPEG-DASH compatibile.

I giocatori consigliati per i test includono:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Lettore Shaka](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [HTTP Live Streaming nativo Apple](https://developer.apple.com/streaming/)
* Lettore video HTML5 integrato Edge, Chrome o Safari
* Lettori commerciali che supportano la riproduzione HLS o DASH

**Quali sono i limiti per lo streaming di un asset del grafico multimediale?**

Il flusso di un asset attivo o registrato da un grafico multimediale usa lo stesso endpoint di streaming e infrastruttura a scalabilità elevata supportato da servizi multimediali per streaming on demand e Live per media & Entertainment, oltre i clienti di broadcast. Ciò significa che è possibile abilitare in modo rapido e semplice la rete per la distribuzione di contenuti di Azure, Verizon o Akamai per distribuire i contenuti a un pubblico con un numero ridotto di utenti o fino a milioni, a seconda dello scenario.

È possibile distribuire contenuti usando Apple HLS o MPEG-DASH.

## <a name="design-your-ai-model"></a>Progettare il modello di intelligenza artificiale 

**Sono disponibili più modelli di intelligenza artificiale in un contenitore docker. Come è possibile usarli con analisi video in tempo reale?** 

Le soluzioni variano a seconda del protocollo di comunicazione usato dal server di inferenza per comunicare con analisi video in tempo reale. Le sezioni seguenti descrivono il funzionamento di ogni protocollo.

*Usare il protocollo http*:

* Singolo contenitore (lvaExtension singolo):  

   Nel server di inferenza è possibile usare una singola porta ma endpoint diversi per i diversi modelli di intelligenza artificiale. Per un esempio di Python, ad esempio, è possibile usare diversi per `route` modello, come illustrato di seguito: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Quindi, nella distribuzione di analisi video in tempo reale, quando si creano istanze dei grafici, impostare l'URL del server di inferenza per ogni istanza, come illustrato di seguito: 

   prima istanza: URL del server di inferenza =`http://lvaExtension:44000/score/face_detection`<br/>
   seconda istanza: URL del server di inferenza =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > In alternativa, è possibile esporre i modelli di intelligenza artificiale su porte diverse e chiamarli quando si creano istanze dei grafici.  

* Più contenitori: 

   Ogni contenitore viene distribuito con un nome diverso. In precedenza, nel set di documentazione di Live Video Analytics è stato illustrato come distribuire un'estensione denominata *lvaExtension*. A questo punto è possibile sviluppare due contenitori diversi, ognuno con la stessa interfaccia HTTP, che significa che hanno lo stesso `/score` endpoint. Distribuire questi due contenitori con nomi diversi e assicurarsi che entrambi siano in ascolto su *porte diverse*. 

   Ad esempio, un contenitore denominato `lvaExtension1` è in ascolto della porta `44000` e un secondo contenitore denominato `lvaExtension2` è in ascolto della porta `44001` . 

   Nella topologia di analisi video live si creano due grafici con URL di inferenza diversi, come illustrato di seguito: 

   Prima istanza: URL del server di inferenza = `http://lvaExtension1:44001/score`    
   Seconda istanza: URL del server di inferenza = `http://lvaExtension2:44001/score`
   
*Usare il protocollo gRPC*: 

* Con il modulo di analisi video Live 1,0, quando si usa un protocollo gRPC (Remote Procedure Call) generico, l'unico modo per farlo è se il server gRPC espone diversi modelli di intelligenza artificiale tramite porte diverse. In [questo esempio di codice](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json), una singola porta, 44000, espone tutti i modelli Yolo. In teoria, il server gRPC Yolo potrebbe essere riscritto per esporre alcuni modelli sulla porta 44000 e altri sulla porta 45000. 

* Con il modulo di analisi video Live 2,0, viene aggiunta una nuova proprietà al nodo dell'estensione gRPC. Questa proprietà, **extensionConfiguration**, è una stringa facoltativa che può essere usata come parte del contratto gRPC. Quando si dispone di più modelli di intelligenza artificiale inclusi in un singolo server di inferenza, non è necessario esporre un nodo per ogni modello di intelligenza artificiale. Per un'istanza di Graph, il provider di estensioni può invece definire come selezionare i diversi modelli di intelligenza artificiale usando la proprietà **extensionConfiguration** . Durante l'esecuzione, analisi video live passa questa stringa al server di inferenza, che può usarla per richiamare il modello di intelligenza artificiale desiderato. 

**Sto creando un server gRPC intorno a un modello di intelligenza artificiale e desidero essere in grado di supportarne l'uso da più fotocamere o istanze di Graph. Come si compila il server?** 

 Assicurarsi prima di tutto che il server sia in grado di gestire più di una richiesta alla volta o di lavorare in thread paralleli. 

Ad esempio, è stato impostato un numero predefinito di canali paralleli nell' [esempio gRPC di analisi video live](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)seguente: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Nella creazione di istanza del server gRPC precedente, il server può aprire solo tre canali alla volta per ogni videocamera o istanza di topologia Graph. Non provare a connettere più di tre istanze al server. Se si tenta di aprire più di tre canali, le richieste saranno in sospeso fino a quando non viene eliminato un canale esistente.  

L'implementazione del server gRPC precedente viene usata negli esempi di Python. Gli sviluppatori possono implementare il proprio server o usare l'implementazione predefinita precedente per aumentare il numero di lavoro impostato sul numero di fotocamere da usare per i feed video. 

Per configurare e utilizzare più fotocamere, è possibile creare istanze di più istanze di topologia Graph, ciascuna delle quali punta allo stesso o a un server di inferenza diverso (ad esempio, il server indicato nel paragrafo precedente). 

**Desidero essere in grado di ricevere più frame da upstream prima di prendere una decisione di inferenza. Come è possibile abilitarla?** 

Gli [esempi predefiniti](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) correnti funzionano *in modalità senza* stato. Non mantengono lo stato delle chiamate precedenti o anche chi ha chiamato. Ciò significa che più istanze di topologia potrebbero chiamare lo stesso server di inferenza, ma il server non può distinguere chi chiama o lo stato per ogni chiamante. 

*Usare il protocollo http*:

Per gestire lo stato, ogni istanza del chiamante o della topologia Graph, chiama il server di inferenza usando il parametro di query HTTP univoco per il chiamante. Ad esempio, gli indirizzi URL del server di inferenza per ogni istanza sono riportati di seguito:  

1 istanza topologia = `http://lvaExtension:44000/score?id=1`<br/>
seconda istanza di topologia = `http://lvaExtension:44000/score?id=2`

… 

Sul lato server, la route del Punteggio sa chi sta chiamando. Se ID = 1, può mantenerne lo stato separatamente per l'istanza del chiamante o della topologia Graph. È quindi possibile salvare i frame video ricevuti in un buffer. Usare, ad esempio, una matrice o un dizionario con una chiave DateTime e il valore è il frame. È quindi possibile definire il server da elaborare (dedurre) dopo che è stato ricevuto il numero *x* di frame. 

*Usare il protocollo gRPC*: 

Con un'estensione gRPC, ogni sessione è per un singolo feed della fotocamera, pertanto non è necessario specificare un ID. Ora, con la proprietà extensionConfiguration, è possibile archiviare i fotogrammi video in un buffer e definire il server da elaborare (dedurre) dopo che è stato ricevuto il numero *x* di frame. 

**Tutti i ProcessMediaStreams di un determinato contenitore eseguono lo stesso modello di intelligenza artificiale?** 

No. Le chiamate di avvio o di arresto dall'utente finale in un'istanza di Graph costituiscono una sessione o forse si è verificata una disconnessione o una riconnessione della fotocamera. L'obiettivo è quello di salvare in modo permanente una sessione se la fotocamera esegue lo streaming di video. 

* Due fotocamere che inviano video per l'elaborazione creano due sessioni. 
* Una fotocamera che passa a un grafico con due nodi di estensione gRPC crea due sessioni. 

Ogni sessione è una connessione duplex completa tra analisi video in tempo reale e il server gRPC e ogni sessione può avere una pipeline o un modello diverso. 

> [!NOTE]
> In caso di disconnessione o riconnessione della fotocamera, con la fotocamera offline per un periodo oltre i limiti di tolleranza, analisi video in tempo reale apre una nuova sessione con il server gRPC. Non è necessario che il server tenga traccia dello stato in queste sessioni. 

Analisi video in tempo reale aggiunge anche il supporto per più estensioni gRPC per una singola fotocamera in un'istanza di Graph. È possibile usare queste estensioni gRPC per eseguire l'elaborazione di intelligenza artificiale in sequenza, in parallelo o come una combinazione di entrambi. 

> [!NOTE]
> La presenza di più estensioni eseguite in parallelo influirà sulle risorse hardware. Tenere presente questo aspetto quando si sceglie l'hardware adatto alle proprie esigenze di calcolo. 

**Qual è il numero massimo di ProcessMediaStreams simultanei?** 

Il video live Analytics non applica limiti a questo numero.  

**Come è possibile decidere se il server di inferenza deve utilizzare CPU o GPU o qualsiasi altro acceleratore hardware?** 

La decisione dipende dalla complessità del modello di intelligenza artificiale sviluppato e dal modo in cui si desidera utilizzare gli acceleratori della CPU e dell'hardware. Durante lo sviluppo del modello di intelligenza artificiale, è possibile specificare quali risorse devono essere utilizzate dal modello e quali azioni devono essere eseguite. 

**Ricerca per categorie archiviare le immagini con i campi di delimitazione dopo l'elaborazione?** 

Attualmente, le coordinate del riquadro vengono fornite come solo messaggi di inferenza. È possibile creare un flusso di trasmissione MJPEG personalizzato che può usare questi messaggi e sovrapporre i rettangoli di delimitazione nei fotogrammi video.  

## <a name="grpc-compatibility"></a>compatibilità con gRPC 

**Come è possibile sapere quali sono i campi obbligatori per il descrittore del flusso multimediale?** 

A qualsiasi campo a cui non si fornisce un valore viene assegnato un [valore predefinito, come specificato da gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Analisi video in tempo reale usa la versione *proto3* della lingua del buffer del protocollo. Tutti i dati del buffer del protocollo usati dai contratti di analisi video live sono disponibili nei [file del buffer del protocollo](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Come è possibile assicurarsi di utilizzare i file del buffer di protocollo più recenti?** 

È possibile ottenere i file del buffer del protocollo più recenti nel [sito dei file di contratto](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Ogni volta che si aggiornano i file di contratto, saranno in questa posizione. Non esiste un piano immediato per aggiornare i file di protocollo, quindi cercare il nome del pacchetto nella parte superiore dei file per conoscerne la versione. L'indicazione corretta è la seguente: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Eventuali aggiornamenti a questi file incrementeranno il valore "v-value" alla fine del nome. 

> [!NOTE]
> Poiché analisi video Live usa la versione proto3 del linguaggio, i campi sono facoltativi e la versione è compatibile con le versioni precedenti e successive. 

**Quali funzionalità di gRPC sono disponibili per l'uso con analisi video in tempo reale? Quali sono le funzionalità obbligatorie e facoltative?** 

È possibile utilizzare qualsiasi funzionalità gRPC sul lato server, a condizione che il contratto dei buffer di protocollo (protobuf) venga completato. 

## <a name="monitoring-and-metrics"></a>Monitoraggio e metriche

**È possibile monitorare il grafico multimediale sul perimetro usando griglia di eventi di Azure?**

Sì. È possibile utilizzare le metriche Prometheus e pubblicarle nella griglia di eventi. 

**È possibile usare monitoraggio di Azure per visualizzare l'integrità, le metriche e le prestazioni dei grafici multimediali nel cloud o sul perimetro?**

Sì, questo approccio è supportato. Per altre informazioni, vedere [Panoramica delle metriche di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

**Sono disponibili strumenti che consentono di monitorare più facilmente il modulo IoT Edge di servizi multimediali?**

Visual Studio Code supporta l'estensione degli strumenti di Azure, con cui è possibile monitorare facilmente gli endpoint del modulo LVAEdge. È possibile usare questo strumento per avviare rapidamente il monitoraggio dell'endpoint predefinito dell'hub Internet per gli "eventi" e visualizzare i messaggi di inferenza indirizzati dal dispositivo perimetrale al cloud. 

Inoltre, è possibile usare questa estensione per modificare il modulo gemello per il modulo LVAEdge per modificare le impostazioni del grafico multimediale.

Per ulteriori informazioni, vedere l'articolo [monitoraggio e registrazione](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fatturazione e disponibilità

**Come viene fatturato l'analisi video in tempo reale IoT Edge?**

Per informazioni dettagliate sulla fatturazione, vedere [prezzi di servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: Introduzione all'analisi dei video in tempo reale su IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
