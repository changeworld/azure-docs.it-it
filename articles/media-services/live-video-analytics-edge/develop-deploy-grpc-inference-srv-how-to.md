---
title: Sviluppare e distribuire un server di inferenza gRPC-Azure
description: Questo articolo fornisce indicazioni su come sviluppare e distribuire un server di inferenza gRPC.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034861"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Guida alla procedura: sviluppare e distribuire un server di inferenza gRPC

## <a name="overview"></a>Panoramica

Questo articolo illustra come è possibile eseguire il wrapping di uno o più modelli di intelligenza artificiale in un server di inferenza gRPC, in modo che possa essere integrato con analisi video in tempo reale (LVA) tramite l'estensione Graph. 

## <a name="suggested-pre-reading"></a>Letture consigliate

* [Estensioni del grafico multimediale](media-graph-extension-concept.md)
* [Protocollo di estensione gRPC](grpc-extension-protocol.md)
* [Schema dei metadati di inferenza](inference-metadata-schema.md)
* [Introduzione a gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [Guida alla lingua di proto3](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Prerequisiti

* Un dispositivo x86-64 o ARM64 che esegue uno dei [sistemi operativi Linux supportati](../../iot-edge/support.md#operating-systems) o un computer Windows.
* [Installare Docker](https://docs.docker.com/desktop/#download-and-install) nel computer.
* Installare [IOT Edge Runtime](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>passaggi di implementazione di gRPC

Per creare un server di inferenza gRPC e implementarlo come estensione con analisi video in tempo reale, verranno usati i passaggi seguenti:

### <a name="setup"></a>Configurazione:

Eseguire i passaggi necessari per [distribuire il modulo Live Video Analytics e lavorare su un dispositivo IOT Edge](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Passaggi di implementazione di alto livello:

1. Scegliere una delle numerose lingue supportate da gRPC: C#, C++, Dart, go, Java, node, Objective-C, PHP, Python, Ruby.
1. Implementare un server gRPC che comunicherà con analisi video in tempo reale usando [i file proto3](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="Server gRPC che comunicherà con analisi video in tempo reale usando i file proto3":::

    All'interno di questo servizio:
    1. Gestire lo scambio di messaggi di descrizione della sessione tra il server e il client.
    1. Gestire [i messaggi di esempio multimediali](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) e restituire risultati.

        1. Richiamare il motore di inferenza che utilizza un modello sottoposto a training per eseguire inferenze sui messaggi in arrivo.
        1. Ricevere i risultati di inferenza dal motore, assemblarli di nuovo come esempio multimediale e rinviarli a analisi video in tempo reale usando il file [. proto di inferenza](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) .

            In alternativa, richiamare qualsiasi funzione di trasformazione multimediale nell'esempio multimediale.
1. Distribuire l'implementazione del server gRPC. Esistono due modi per eseguire questa operazione:

    1. Distribuire come un modulo di Internet delle cose con percorso condiviso con il modulo di analisi video live
    1. Distribuisci come modulo di Internet delle cose in un nodo accessibile alla rete (in locale o nel cloud) che può scambiare dati con il modulo di analisi video live.
1. Configurare una topologia del grafico di analisi video in tempo reale con il modulo Live Video Analytics e indirizzarlo al server gRPC.

### <a name="recommendation"></a>Raccomandazione:

Quando si colloca nello stesso nodo, è possibile utilizzare la memoria condivisa per ottenere prestazioni ottimali. A questo scopo è necessario usare le funzionalità di memoria condivisa di Linux esposte dal linguaggio di programmazione/ambiente.

1. Aprire l'handle di memoria condivisa di Linux.
1. Alla ricezione di un frame, accedere all'offset dell'indirizzo all'interno della memoria condivisa.
1. Confermare il completamento dell'elaborazione del frame in modo che la memoria possa essere recuperata da analisi video in tempo reale.

## <a name="create-a-grpc-inference-server"></a>Creare un server di inferenza gRPC

A questo punto si creerà un modulo IoT Edge (intelligenza artificiale esterna) che accetta i fotogrammi video dall'analisi dei video in tempo reale usando i messaggi [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) tramite la memoria condivisa, classificare i frame come "scuri" o "leggeri" e restituire i risultati di inferenza al sink di messaggi dell'hub Internet in analisi video live usando lo [schema dei metadati di inferenza](inference-metadata-schema.md).

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="compilare un modulo di IoT Edge (intelligenza artificiale esterna)":::

Questo server di inferenza gRPC è un'applicazione console .NET Core compilata che gestisce i messaggi [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) inviati tra l'analisi video in tempo reale e l'intelligenza artificiale personalizzata. Di seguito è riportato il flusso dei messaggi tra analisi video in tempo reale e il server di inferenza gRPC:

1. Analisi video in tempo reale Invia un descrittore di flussi multimediali (vedere [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)) che definisce le informazioni sul flusso multimediale che verranno inviate, seguite dai fotogrammi video al server come messaggio [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) sulla sessione del flusso gRPC. 
1. Il server convalida e riconosce il descrittore del flusso e imposta il metodo di trasferimento dei dati desiderato.
1. Analisi video in tempo reale inizia quindi a inviare i file MediaSample che contengono i fotogrammi video.
1. Il server analizza i fotogrammi video ricevuti e inizia a elaborarli usando un processore di immagini definito dall'utente.
1. Il server restituisce quindi i risultati dell'inferenza come messaggi [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) non appena sono disponibili. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Creare un server di inferenza gRPC":::

I fotogrammi video possono essere trasferiti tramite [memoria condivisa](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) oppure possono essere incorporati all'interno del messaggio protobuf. La modalità di trasferimento dei dati può essere configurata nella topologia Graph LVA per determinare il modo in cui verranno trasferiti i frame. Questa operazione viene eseguita configurando l'elemento **DataTransfer** della proprietà MediaGraphGrpcExtension come mostrato di seguito:

Embedded

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Memoria condivisa:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Quando si comunica con la memoria condivisa, il valore di IpcMode deve essere impostato su **Shareable** e nel modulo del server gRPC impostare il valore di IpcMode su **container: {CONTAINER_NAME}**. Queste impostazioni devono essere apportate nel file manifesto di distribuzione usato per la distribuzione dei moduli nell'hub Azure. Di seguito è riportato un esempio delle opzioni del contenitore da usare per la configurazione dei moduli IoT Edge.

Modulo di analisi video Live:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

modulo di estensione gRPC:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Verificare che sia possibile accedere all'area di memoria condivisa di "container: lvaEdge" all'interno di grpcExtension.

## <a name="sample-grpc-server"></a>Server gRPC di esempio

Per comprendere i dettagli del modo in cui viene sviluppato il server gRPC, esaminiamo l'esempio di codice.

1. Clonare il repository dal collegamento GitHub [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Avviare VSCode e passare alla cartella/src/edge/modules/grpcExtension.
1. Di seguito viene illustrata una rapida procedura dettagliata dei file:

    1. **Program. cs**: è il punto di ingresso dell'applicazione. È responsabile dell'inizializzazione e della gestione del server gRPC, che fungerà da host. In questo esempio, la porta per restare in attesa dei messaggi gRPC in ingresso da un client di gRPC (ad esempio, analisi video in tempo reale) viene specificata dall'elemento di configurazione grpcBindings nel AppConfig.json.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: questa classe è responsabile della gestione dei messaggi [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) . Il frame verrà letto nel messaggio, verrà richiamato il ImageProcessor e i risultati dell'inferenza vengono scritti.
Ora che sono state configurate e inizializzate le connessioni alla porta del server gRPC, esaminiamo in che modo è possibile elaborare i messaggi gRPC in arrivo.

        * Una volta stabilita una sessione gRPC, il primo messaggio che il server gRPC riceverà dal client (analisi video in tempo reale) è un MediaStreamDescriptor definito nel file [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) . 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * Nell'implementazione del server, il metodo `ProcessMediaStreamDescriptor` convaliderà la proprietà MediaDescriptor di MediaStreamDescriptor per un file video e configurerà la modalità di trasferimento dei dati (che usa la memoria condivisa o la modalità di trasferimento frame incorporata), a seconda delle impostazioni specificate nella topologia e del file del modello di distribuzione usato. 
        * Quando si riceve il messaggio e si configura correttamente la modalità di trasferimento dei dati, il server gRPC restituisce il messaggio MediaStreamDescriptor al client come riconoscimento e pertanto stabilisce una connessione tra il server e il client.    
        * Dopo che l'analisi video Live ha ricevuto il riconoscimento, inizierà a trasferire il flusso multimediale al server gRPC. Nell'implementazione del server, il metodo `ProcessMediaStream` elaborerà il MediaStreamMessage in ingresso. MediaStreamMessage è definito anche nell' [estensione. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * A seconda del valore di batchSize nella Appconfig.jssu, il server continuerà a ricevere i messaggi e archivierà i frame video in un elenco. Una volta raggiunto il limite di batchSize, la funzione chiamerà la funzione o il file che elaborerà l'immagine. In questo caso, il metodo chiama un file denominato BatchImageProcessor. cs
    1. **Processors\BatchImageProcessor.cs**: questa classe è responsabile dell'elaborazione delle immagini. In questo esempio è stato usato un modello di classificazione delle immagini. Per ogni immagine che verrà elaborata, l'algoritmo utilizzato è il seguente:

        1. Converte l'immagine in una matrice di byte per l'elaborazione. Vedere il metodo: `GetBytes(Bitmap image)`
        
            Il processore di esempio usato supporta solo frame di immagini con codifica JPG e nessuno come formato pixel. Nel caso in cui il processore personalizzato supporti una codifica e/o un formato diversi, aggiornare il `IsMediaFormatSupported` metodo della classe Processor.
        1. Utilizzando la [classe ColorMatrix](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1), convertire l'immagine in scala di grigi. Vedere il metodo: `ToGrayScale(Image source)` .
        1. Una volta ottenuto l'immagine della scala di grigi, viene calcolata la media dei byte della scala di grigi.
        1. Se il valore medio < 127, l'immagine viene classificata come "scura". in caso contrario, verrà classificata come "Light" con valore di confidenza come 1,0. Vedere il metodo: `ProcessImage(List<Image> images)` .

    È possibile aggiungere la logica del processore modificando questa classe o aggiungendo una nuova classe e implementando il metodo:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Una volta aggiunta la nuova classe, è necessario aggiornare MediaGraphExtensionService. cs in modo da creare un'istanza della classe e richiamare il metodo ProcessImage su di essa per eseguire la logica di elaborazione. 

## <a name="connect-with-live-video-analytics-module"></a>Connetti con modulo di analisi video live

Ora che è stato creato il modulo di estensione gRPC, verrà ora creata e distribuita la topologia del grafico multimediale.

1. In Visual Studio Code seguire [queste istruzioni](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) per accedere a Docker.
1. In Visual Studio Code passare a src/edge. Si noteranno il file .env e alcuni file modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo Edge. Include alcuni valori segnaposto. Il file .env include i valori per queste variabili.
    
    Quindi selezionare Build and Push IoT Edge Solution (Crea ed esegui il push della soluzione IoT Edge). Usare src/Edge/deployment.grpc.template.json per questo passaggio.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Connetti con modulo di analisi video live":::
    
    Questa azione compila il modulo server grpc ed esegue il push dell'immagine nel Container Registry di Azure.
    Verificare che le variabili di ambiente CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr siano definite nel file .env.
1. Passare alla cartella src/cloud-to-device-console-app. Sono inclusi il file appsettings.json e alcuni altri file:

    * c2d-console-app.csproj: il file di progetto per Visual Studio Code.
    * operations.json: un elenco di operazioni che il programma dovrà eseguire.
    * Program.cs: il codice del programma di esempio. Questo codice:

        * Carica le impostazioni dell'app.
        * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md).
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra TERMINAL e gli eventi generati dal modulo nella finestra OUTPUT.
        * Richiama i metodi diretti per pulire le risorse.
1. Modificare il file operations.json:

    * Cambiare il collegamento alla topologia del grafo:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * In GraphInstanceSet modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * In GraphTopologyDelete modificare il nome:<br/>`"name": "InferencingWithGrpcExtension"`

            La topologia (ad esempio, `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) deve definire un indirizzo di estensione:
    * Parametro address dell'estensione

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Configurazione

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo Edge e le relative impostazioni di configurazione. Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo Edge.
Questo passaggio crea il manifesto di distribuzione IoT Edge in src/Edge/config/deployment.grpc.amd64.json. Fare clic con il pulsante destro del mouse sul file e scegliere **Crea distribuzione per singolo dispositivo**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Generare e distribuire il manifesto della distribuzione di IoT Edge":::

A questo punto, Visual Studio Code chiede di selezionare un dispositivo hub IoT. Selezionare il dispositivo IoT Edge, che sarà lva-sample-device.
In questa fase è stata avviata la distribuzione dei moduli Edge nel dispositivo IoT Edge. Dopo circa 30 secondi aggiornare l'hub IoT di Azure nella sezione in basso a sinistra in Visual Studio Code. Si noterà che è stato distribuito un nuovo modulo denominato lvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="È stato distribuito un nuovo modulo denominato lvaExtension":::

## <a name="next-steps"></a>Passaggi successivi

Per eseguire l'esempio e interpretare i risultati, seguire i passaggi relativi alla **preparazione per il monitoraggio degli eventi** indicati nella Guida introduttiva all' [analisi dei video in tempo reale con il modello](use-your-model-quickstart.md) . Vedere anche le topologie di esempio gRPC: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension e [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).