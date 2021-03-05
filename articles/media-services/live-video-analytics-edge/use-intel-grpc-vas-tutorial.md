---
title: Analizza i video live usando Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale tramite gRPC
description: Questa esercitazione illustra come usare l'estensione Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale di Intel per analizzare un feed video live da una fotocamera IP (simulata).
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 282ab76c34405f19e7f81b2a8069c178b17ee0c1
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124415"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Esercitazione: analizzare video in tempo reale usando Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale 

Questa esercitazione illustra come usare l'estensione Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale di Intel per analizzare un feed video live da una fotocamera IP (simulata). Si vedrà come questo server di inferenza consente di accedere a modelli diversi per il rilevamento di oggetti (una persona, un veicolo o una bicicletta), la classificazione degli oggetti (attribuzioni del veicolo) e un modello per il rilevamento degli oggetti (persona, veicolo e bicicletta). L'integrazione con il modulo gRPC consente di inviare fotogrammi video al server di inferenza di intelligenza artificiale. I risultati vengono quindi inviati all'hub IoT Edge. Quando si esegue questo servizio di inferenza nello stesso nodo di calcolo di analisi video in tempo reale, è possibile sfruttare i vantaggi dell'invio di dati video tramite memoria condivisa. Ciò consente di eseguire l'inferenza alla frequenza dei fotogrammi del feed video live, ad esempio 30 fotogrammi/sec). 

Questa esercitazione usa una VM di Azure come dispositivo IoT Edge e un flusso video live simulato. Si basa sul codice di esempio scritto in C# e si basa sull'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md).

> [!NOTE]
> Questa esercitazione richiede l'uso di un computer x86-64 come dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha un account, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  > [!NOTE]
  > È necessaria una sottoscrizione di Azure con le autorizzazioni per la creazione di entità servizio. il **ruolo di proprietario** lo fornisce. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account per concedere le autorizzazioni appropriate. 
* [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se non si è completata la guida di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md), assicurarsi di seguire la procedura per [configurare le risorse di Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Quando si installa Azure IoT Tools, è possibile che venga chiesto di installare Docker. È possibile ignorare questa richiesta.

## <a name="review-the-sample-video"></a>Esaminare il video di esempio

Quando si configurano le risorse di Azure, nella VM Linux di Azure usata come dispositivo IoT Edge viene copiato un breve video di un parcheggio. In questa guida il file video viene usato per simulare uno streaming live.

Aprire un'applicazione, ad esempio il [lettore multimediale VLC](https://www.videolan.org/vlc/). Premere CTRL+N, quindi incollare un collegamento al [video](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) per avviare la riproduzione. Verrà visualizzato il filmato dei veicoli in un parcheggio, con la maggior parte di essi parcheggiati e uno in movimento.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

In questa Guida introduttiva si userà l'analisi video in tempo reale su IoT Edge insieme all'estensione Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale da Intel per rilevare gli oggetti, ad esempio i veicoli, per classificare i veicoli o tenere traccia di veicoli, persone o biciclette. Si pubblicheranno quindi gli eventi di inferenza risultanti nell'hub di IoT Edge.

## <a name="overview"></a>Panoramica

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Panoramica di LVA MediaGraph":::

Il diagramma mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo perimetrale](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una fotocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i frame video al nodo del [processore dell'estensione gRPC](media-graph-concept.md#grpc-extension-processor) . 

Il nodo del processore di estensione gRPC accetta i frame video decodificati come input e li inoltra a un endpoint [gRPC](terminology.md#grpc) esposto da un server gRPC. Il nodo supporta il trasferimento dei dati mediante la [memoria condivisa](https://en.wikipedia.org/wiki/Shared_memory) o l'incorporamento diretto del contenuto nel corpo dei messaggi gRPC. Inoltre, il nodo dispone di un formattatore di immagine incorporato per la scalabilità e la codifica dei fotogrammi video prima che vengano inoltrati all'endpoint gRPC. Scaler dispone di opzioni per le proporzioni dell'immagine da mantenere, imbottire o allungare. Il codificatore di immagini supporta formati JPEG, PNG o BMP. Altre informazioni sul processore sono disponibili [qui](media-graph-extension-concept.md#grpc-extension-processor).

In questa esercitazione si apprenderà come:

1. Distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Informazioni su Intel OpenVINO™ DL Streamer-Edge AI Extension Module


Il modulo di estensione di OpenVINO™ DL Streamer-Edge per intelligenza artificiale è un microservizio basato sul servizio di analisi video di Intel, che fornisce le pipeline di analisi video compilate con OpenVINO™ DL Streamer. Gli sviluppatori possono inviare fotogrammi video decodificati al modulo di estensione AI che esegue il rilevamento, la classificazione o il rilevamento e restituisce i risultati. Il modulo di estensione AI espone le API di gRPC compatibili con le piattaforme di analisi video, ad esempio analisi video in tempo reale su IoT Edge da Microsoft. 

Per creare soluzioni di analisi video live complesse e a prestazioni elevate, il modulo Analisi video live in IoT Edge dovrà essere associato a un potente motore di inferenza che possa sfruttare la scalabilità a livello perimetrale. In questa esercitazione le richieste di inferenza vengono inviate all' [estensione Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale](https://aka.ms/lva-intel-openvino-dl-streamer), un modulo perimetrale progettato per funzionare con analisi video in tempo reale su IOT Edge. 

Nella versione iniziale di questo server di inferenza è possibile accedere ai [modelli](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) seguenti:

- object_detection per il ![ rilevamento di oggetti person_vehicle_bike_detection per il veicolo](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification per vehicle_attributes_recognition ![ classificazione degli oggetti per il veicolo](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking per il ![ rilevamento degli oggetti person_vehicle_bike_tracking per il veicolo da persona](./media/use-intel-openvino-tutorial/object-tracking.png)

Usa il rilevamento degli oggetti precaricato, la classificazione degli oggetti e le pipeline di rilevamento degli oggetti per iniziare rapidamente. Inoltre, viene fornita con i modelli precaricati [Person-Vehicle-bike-Detection-Crossroads-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) e [Vehicle-Attributes-Recognition-Barrier-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md).

> [!NOTE]
> Scaricando e usando il modulo perimetrale: OpenVINO™ DL Streamer-Edge per intelligenza artificiale di Intel e il software incluso, accetti i termini e le condizioni del [contratto di licenza](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel si impegna a rispettare i diritti umani ed evitare di essere complice nelle violazioni di tali diritti. Vedere i [principi dei diritti umani su scala globale di Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). I prodotti e il software di Intel sono destinati esclusivamente all'uso in applicazioni che non causano violazioni dei diritti umani riconosciuti a livello internazionale né vi contribuiscono.

È possibile usare la flessibilità delle diverse pipeline per il caso d'uso specifico modificando semplicemente le variabili di ambiente della pipeline nel modello di distribuzione. Questo consente di modificare rapidamente il modello di pipeline e, in combinazione con l'analisi dei video in tempo reale, è un aspetto di pochi secondi per modificare la pipeline multimediale e il modello di inferenza.  

## <a name="create-and-deploy-the-media-graph"></a>Creare e distribuire il grafico multimediale

### <a name="examine-and-edit-the-sample-files"></a>Esaminare e modificare i file di esempio

Come parte dei prerequisiti, il codice di esempio è stato scaricato in una cartella. Seguire questa procedura per esaminare e modificare i file di esempio.

1. In Visual Studio Code passare a *src/edge*. Si noteranno il file *.env* e alcuni file modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo Edge. Include alcuni valori segnaposto. Il file *.env* include i valori per queste variabili.

1. Passare alla cartella *src/cloud-to-device-console-app*. Sono inclusi il file *appsettings.json* e alcuni altri file:

    * ***c2d-console-app.csproj***: il file di progetto per Visual Studio Code.
    * ***operations.json***: un elenco di operazioni che il programma dovrà eseguire.
    * ***Program.cs***: il codice del programma di esempio. Questo codice:

        * Carica le impostazioni dell'app.
        * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md).
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINAL** e gli eventi generati dal modulo nella finestra **OUTPUT**.
        * Richiama i metodi diretti per pulire le risorse.


1. Modificare il file *operations.json*:
    * Cambiare il collegamento alla topologia del grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * In `GraphInstanceSet` modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * In `GraphTopologyDelete` modificare il nome:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge

1. Fare clic con il pulsante destro del mouse sul file *src/Edge/deployment.openvino.grpc.cpu.template.js* e quindi selezionare **genera IOT Edge manifesto della distribuzione**.

    ![Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge)](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Il *deployment.openvino.grpc.cpu.amd64.js* nel file manifesto viene creato nella cartella *src/Edge/config* .

> [!NOTE]
È stato inoltre incluso un *deployment.openvino.grpc.gpu.template.jssu* un modello che Abilita il supporto GPU per il modulo di estensione Intel OpenVINO DL Streamer-Edge per intelligenza artificiale. Questi modelli puntano all'immagine dell'hub Docker di Intel.

I modelli indicati in precedenza puntano all'immagine dell'hub Docker Intel. Se invece si vuole ospitare una copia nella propria Container Registry di Azure, è possibile seguire i passaggi 1 e 2 seguenti:
1. Eseguire SSH in un dispositivo con strumenti dell'interfaccia della riga di comando di Docker installati (ovvero il dispositivo perimetrale) e pull/Tag/push del contenitore con questi passaggi:
    * Estrarre l'immagine di Intel dall'hub docker:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Contrassegnare l'immagine di Intel con il nome Container Registry di Azure. Sostituire {YOUR ACR NAME} con il nome di ACR, disponibile nel file con estensione ENV:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Eseguire il push dell'immagine con tag nel Container Registry di Azure:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. A questo punto è necessario modificare i modelli per fare riferimento alla nuova immagine ospitata in Azure Container Registry.
    * Fare clic con il pulsante destro del mouse *sulladeployment.openvino.grpc.cpu.template.js* e passare alla parte del modulo *lavExtension* e sostituire:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        con:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Ripetere il passaggio 2 per il *deployment.openvino.grpc.gpu.template.js*


3. Se è stato completato l'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md), ignorare questo passaggio. 

    In caso contrario, accanto al riquadro **HUB IOT DI AZURE** nell'angolo in basso a sinistra selezionare l'icona **Altre azioni** e quindi selezionare **Set IoT Hub Connection String** (Imposta la stringa di connessione dell'hub IoT). È possibile copiare la stringa dal file *appsettings.json*. In alternativa, per assicurarsi di aver configurato l'hub IoT appropriato in Visual Studio Code, usare il comando [Selezionare un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Impostare la stringa di connessione dell'hub IoT](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Potrebbe essere richiesto di fornire informazioni di endpoint predefinite per l'hub Internet delle cose. Per ottenere tali informazioni, in portale di Azure passare all'hub Internet e cercare l'opzione **endpoint predefiniti** nel riquadro di spostamento a sinistra. Fare clic qui e cercare l' **endpoint compatibile con l'hub eventi** nella sezione **endpoint compatibile con hub eventi** . Copiare e usare il testo nella casella. L'endpoint sarà simile al seguente:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Fare clic con *il pulsante destro del mouse su src/Edge/config/deployment.openvino.grpc.cpu.template.json* e scegliere **Crea distribuzione per singolo dispositivo**. 

    ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quando viene chiesto di selezionare un dispositivo hub IoT, selezionare **lva-sample-device**.
1. Dopo circa 30 secondi, nell'angolo in basso a sinistra della finestra aggiornare l'hub IoT di Azure. Il dispositivo Edge mostra ora i moduli distribuiti seguenti:

    * Il modulo Analisi video live, denominato **lvaEdge**
    * Il modulo **rtspsim**, che simula un server RTSP e funge da origine di un feed video live
    * Il modulo **lvaExtension** , che è l'estensione Intel OPENVINO™ DL Streamer-Edge per intelligenza artificiale 

### <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

Fare clic con il pulsante destro del mouse sul dispositivo di Analisi video live e scegliere **Avvia monitoraggio endpoint eventi predefinito**. Questo passaggio è necessario per monitorare gli eventi dell'hub IoT nella finestra **OUTPUT** di Visual Studio Code. 

![Avviare il monitoraggio](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Eseguire il programma di esempio per rilevare veicoli, persone o biciclette
Se si apre la [topologia Graph](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) per questa esercitazione in un browser, si noterà che il valore di `grpcExtensionAddress` è stato impostato su `tcp://lvaExtension:5001` , rispetto all'esempio *httpExtensionOpenVINO* non è necessario modificare l'URL nel server gRPC. Si indica invece al modulo di eseguire una pipeline specifica dalle variabili di ambiente come indicato in precedenza. Nel modello predefinito è stato impostato su: "object_detection" per "person_vehicle_bike_detection". È possibile sperimentare altre pipeline supportate. 

1. In Visual Studio Code aprire la scheda **Estensioni** (oppure premere CTRL+MAIUSC+X) e cercare Hub IoT di Azure.
1. Fare clic con il pulsante destro del mouse e scegliere **Impostazioni estensione**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Impostazioni estensione":::
1. Cercare e abilitare "Show Verbose Message" (Visualizza messaggio dettagliato).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message":::
1. Per avviare la sessione di debug, premere F5. Verranno visualizzati i messaggi stampati nella finestra **TERMINALE**.
1. Il codice *operations.json* verrà avviato con le chiamate ai metodi diretti `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere INVIO.

    La finestra **TERMINALE** mostra il set successivo di chiamate ai metodi diretti:

     * Una chiamata a `GraphTopologySet` che usa `topologyUrl` precedente
     * Una chiamata a `GraphInstanceSet` che usa il corpo seguente:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```

     * Una chiamata a `GraphInstanceActivate` che avvia l'istanza del grafo e il flusso di video
     * Una seconda chiamata a `GraphInstanceList` che mostra che l'istanza del grafo è in esecuzione
1. L'output della finestra **TERMINALE** viene sospeso in corrispondenza di una richiesta `Press Enter to continue`. Non premere ancora INVIO. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Passare alla finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati i messaggi inviati dal modulo Analisi video live in IoT Edge all'hub IoT. La sezione seguente di questo argomento di avvio rapido descrive questi messaggi.
1. Il grafo multimediale continua a essere eseguito e a stampare i risultati. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Quindi, per arrestare il grafo multimediale, tornare nella finestra **TERMINALE** e premere INVIO. 

    La serie successiva di chiamate pulisce le risorse:
      * Una chiamata a `GraphInstanceDeactivate` disattiva l'istanza del grafo.
      * Una chiamata a `GraphInstanceDelete` elimina l'istanza.
      * Una chiamata a `GraphTopologyDelete` elimina la topologia.
      * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è vuoto.

## <a name="interpret-results"></a>Interpretare i risultati

Quando si esegue il grafo multimediale, i risultati del nodo del processore di estensioni HTTP vengono inviati all'hub IoT tramite il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** contengono una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo. 

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. Se la connessione riesce, viene stampato l'evento seguente. Il tipo di evento è **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

In questo messaggio si notino i dettagli seguenti:

* Il messaggio è un evento di diagnostica. `MediaSessionEstablished` indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* In `applicationProperties` `subject` indica che il messaggio è stato generato dal nodo di origine RTSP nel grafo multimediale.
* In `applicationProperties` `eventType` indica che questo evento è un evento di diagnostica.
* `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* `body` contiene dati sull'evento di diagnostica. In questo caso i dati sono costituiti dai dettagli di [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento di inferenza

Il nodo processore dell'estensione gRPC riceve i risultati dell'inferenza dall'estensione Intel OpenVINO™ DL Streamer-Edge per intelligenza artificiale. Genera quindi i risultati tramite il nodo sink dell'hub IoT come eventi di inferenza. 

In questi eventi il tipo è impostato su `entity` per indicare che si tratta di un'entità, ad esempio un'automobile o un camion. Il valore di `eventTime` corrisponde all'ora UTC in cui l'oggetto è stato rilevato. 

Nell'esempio seguente viene identificato un veicolo, il tipo di veicolo (van) e il colore (bianco), con un livello di confidenza superiore a 0,9, assegna anche un ID all'entità quando si usa il modello di rilevamento degli oggetti.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
    }
}
```

Nei messaggi si notino i dettagli seguenti:

* In `applicationProperties` `subject` fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. 
* In `applicationProperties` `eventType` indica che questo evento è un evento di analisi.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* La sezione `body` contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza e quindi il corpo contiene dati di tipo `inferences`.
* La sezione `inferences` indica che `type` è `entity`. Questa sezione include dati aggiuntivi sull'entità.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Eseguire il programma di esempio per rilevare persone, veicoli o biciclette
Per usare un modello diverso, sarà necessario modificare il modello di distribuzione. Per passare tra i modelli supportati è possibile modificare le variabili di ambiente situate nel modulo lvaExtenstion. Per i valori e le combinazioni supportate per i modelli, vedere questo [documento su GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) .

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Copiare il modello e archiviarlo con un nuovo nome per ogni possibile pipeline. In questo modo è possibile passare da un modello all'altro creando una nuova distribuzione basata su uno di questi modelli.

Dopo aver modificato le variabili, è possibile distribuire di nuovo il modello nel dispositivo. È ora possibile ripetere i passaggi precedenti per eseguire nuovamente il programma di esempio, con la nuova pipeline. I risultati dell'inferenza saranno simili (nello schema) ma visualizzeranno più o meno informazioni a seconda del modello di pipeline scelto.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare altre guide di avvio rapido o esercitazioni, mantenere le risorse create. In caso contrario, passare al portale di Azure e quindi ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita questa guida di avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Esaminare altri problemi per utenti avanzati:

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP invece del simulatore RTSP. È possibile cercare videocamere IP che supportano RTSP nella pagina dei prodotti [conformi a ONVIF](https://www.onvif.org/conformant-products/). Cercare dispositivi conformi ai profili G, S o T.
* Usare un dispositivo Linux Intel x64 anziché una VM Linux di Azure. Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge.md). Quindi seguire il dispositivo con l'hub IoT di Azure seguendo le istruzioni riportate in [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md).
