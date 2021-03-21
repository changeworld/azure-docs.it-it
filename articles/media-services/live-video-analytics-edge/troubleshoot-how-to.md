---
title: Risolvere i problemi di analisi video in tempo reale su IoT Edge-Azure
description: Questo articolo illustra i passaggi per la risoluzione dei problemi di analisi video in tempo reale su IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d766843f58bc2cdd0dcdddfad337b23fefb28768
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698740"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Risolvere i problemi di analisi video in tempo reale su IoT Edge

Questo articolo illustra i passaggi per la risoluzione dei problemi di analisi video in tempo reale (LVA) in Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

### <a name="diagnostics"></a>Diagnostica

Come parte della distribuzione di analisi video in tempo reale, è possibile configurare le risorse di Azure, ad esempio i dispositivi IoT Edge e l'hub. Come primo passaggio per diagnosticare i problemi, assicurarsi sempre che il dispositivo perimetrale sia configurato correttamente seguendo queste istruzioni:

1. [Eseguire il `check` comando](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Controllare la versione del IOT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Verificare lo stato di IOT Edge Security Manager e dei relativi log](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Visualizzare i messaggi che passano attraverso l'hub IOT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Riavviare i contenitori](../../iot-edge/troubleshoot.md#restart-containers).
1. [Controllare le regole di configurazione del firewall e delle porte](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemi di pre-distribuzione

Se l'infrastruttura perimetrale è corretta, è possibile cercare problemi con il file manifesto di distribuzione. Per distribuire l'analisi video in tempo reale sul modulo IoT Edge sul dispositivo IoT Edge insieme a tutti gli altri moduli Internet, si usa un manifesto di distribuzione che contiene l'hub IoT Edge, l'agente IoT Edge e altri moduli e le relative proprietà. È possibile usare il comando seguente per distribuire il file manifesto:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Se il formato del codice JSON non è corretto, è possibile che venga visualizzato l'errore seguente:   
&nbsp;&nbsp;&nbsp;**Non è stato possibile analizzare JSON dal file:' <deployment manifest.json> ' per l'argomento ' content ' con eccezione: "dati aggiuntivi: riga 101 colonna 1 (Char 5325)"**

Se si verifica questo errore, è consigliabile controllare il codice JSON per le parentesi mancanti o altri problemi con la struttura del file. Per convalidare la struttura dei file, è possibile usare un client come il [plug-in Notepad + + con il Visualizzatore JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) o uno strumento online come il [formattatore JSON & validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Durante la distribuzione: diagnostica con i metodi diretti del grafico multimediale 

Dopo che l'analisi video in tempo reale su IoT Edge modulo è stata distribuita correttamente nel dispositivo IoT Edge, è possibile creare ed eseguire il grafico multimediale richiamando i [metodi diretti](direct-methods.md).  
>[!NOTE]
>  Le chiamate dirette al metodo devono essere apportate **`lvaEdge`** solo al modulo.

È possibile usare la portale di Azure per eseguire una diagnosi del grafico multimediale usando metodi diretti:

1. Nella portale di Azure passare all'hub Internet delle cose connesso al dispositivo IoT Edge.

1. Cercare **gestione automatica dispositivi**, quindi selezionare **IOT Edge**.  

1. Nell'elenco dei dispositivi perimetrali selezionare il dispositivo che si desidera diagnosticare.  
         
    ![Screenshot della portale di Azure visualizzazione di un elenco di dispositivi perimetrali](./media/troubleshoot-how-to/lva-sample-device.png)


1. Verificare se il codice di risposta è *200-OK*. Altri codici di risposta per il [runtime di IOT Edge](../../iot-edge/iot-edge-runtime.md) includono:
    * 400 - La configurazione della distribuzione è in formato non corretto o non valida.
    * 417: il dispositivo non ha un set di configurazione della distribuzione.
    * 412 - La versione dello schema nella configurazione della distribuzione non è valida.
    * 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
    * 500 - Si è verificato un errore nel runtime di IoT Edge.

    > [!TIP]
    > Se si verificano problemi durante l'esecuzione di Azure IoT Edge moduli nell'ambiente, usare **[Azure IOT Edge passaggi di diagnostica standard](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** come guida per la risoluzione dei problemi e la diagnostica.
### <a name="post-deployment-direct-method-error-code"></a>Post-distribuzione: codice di errore diretto del metodo
1. Se si ottiene uno stato `501 code` , verificare che il nome del metodo diretto sia accurato. Se il nome del metodo e il payload della richiesta sono accurati, è necessario ottenere risultati con codice di esito positivo = 200. 
1. Se il payload della richiesta non è accurato, si otterranno uno stato `400 code` e un payload di risposta che indica il codice di errore e il messaggio che dovrebbero aiutare a diagnosticare il problema con la chiamata al metodo diretto.
    * Il controllo delle proprietà segnalate e desiderate consente di comprendere se le proprietà del modulo sono state sincronizzate con la distribuzione. In caso contrario, è possibile riavviare il dispositivo IoT Edge. 
    * Usare la guida ai [metodi diretti](direct-methods.md) per chiamare alcuni metodi, soprattutto quelli semplici, ad esempio GraphTopologyList. La guida specifica inoltre i payload di richiesta e risposta previsti e i codici di errore. Una volta completati i semplici metodi diretti, è possibile assicurarsi che il modulo di analisi di video in tempo reale IoT Edge sia accettabile dal punto di vista funzionale.
        
       ![Screenshot del riquadro "metodo diretto" per il modulo IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Se l' **oggetto specificato nella distribuzione** e **segnalato dalle** colonne del dispositivo indica *Sì*, è possibile richiamare i metodi diretti sul modulo di analisi video live in IOT Edge. Selezionare il modulo per passare a una pagina in cui è possibile controllare le proprietà desiderate e segnalate e richiamare i metodi diretti. Tenere presente quanto segue: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Post-distribuzione: diagnostica i registri per i problemi durante l'esecuzione 

I log dei contenitori per il modulo di IoT Edge devono contenere informazioni di diagnostica per facilitare il debug dei problemi durante il runtime del modulo. È possibile [controllare i log dei contenitori per individuare eventuali problemi](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) e diagnosticare autonomamente il problema. 

Se sono stati eseguiti tutti i controlli precedenti e continuano a verificarsi problemi, raccogliere i log dal dispositivo IoT Edge [con il `support bundle` comando](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) per un'ulteriore analisi da parte del team di Azure. È possibile [contattarci](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per assistenza e per inviare i log raccolti.

## <a name="common-error-resolutions"></a>Risoluzioni di errori comuni

L'analisi video in tempo reale viene distribuita come modulo IoT Edge sul dispositivo IoT Edge e collabora con i moduli di IoT Edge Agent e Hub. Alcuni degli errori comuni che si verificano con la distribuzione di analisi video in tempo reale sono causati da problemi con l'infrastruttura di Internet delle cose sottostante. Gli errori includono:

* [L'agente di IOT Edge si interrompe dopo circa un minuto](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [L'agente di IOT Edge non è in grado di accedere all'immagine di un modulo (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Il modulo dell'agente di IOT Edge segnala "file di configurazione vuoto" e non vengono avviati moduli nel dispositivo](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Non è possibile avviare l'hub IOT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Il daemon di sicurezza IoT Edge ha esito negativo con un nome host non valido](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [L'analisi video in tempo reale o qualsiasi altro modulo di IOT Edge personalizzato non riesce a inviare un messaggio all'Hub Edge con errore 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Il modulo IOT Edge viene distribuito correttamente, quindi scompare dal dispositivo](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Se si verificano problemi durante l'esecuzione di Azure IoT Edge moduli nell'ambiente, usare **[Azure IOT Edge passaggi di diagnostica standard](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** come guida per la risoluzione dei problemi e la diagnostica.

Potrebbero verificarsi problemi anche durante l'esecuzione dello **[script di configurazione delle risorse di analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)**. Alcuni problemi comuni includono:

* Utilizzo di una sottoscrizione in cui non si dispone dei privilegi di proprietario. In questo modo lo script avrà esito negativo con un errore **ForbiddenError** o **AuthorizationFailed** .
    * Per superare questo problema, assicurarsi di disporre dei privilegi di **proprietario** per la sottoscrizione che si intende usare. Se non è possibile eseguire questa operazione da soli, rivolgersi all'amministratore della sottoscrizione per concedere i privilegi appropriati.
* **La distribuzione del modello non è riuscita a causa di una violazione dei criteri.**
    * Per superare questo problema, collaborare con l'amministratore IT per assicurarsi che le chiamate creino una macchina virtuale per ignorare il blocco dell'autenticazione SSH. Questa operazione non sarà necessaria perché si usa una rete Bastion sicura che richiede un nome utente e una password per comunicare con le risorse di Azure. Queste credenziali vengono archiviate nel file di **vm-edge-device-credentials.txt~/CloudDrive/LVA-Sample/** in cloud Shell, una volta che la macchina virtuale è stata creata, distribuita e collegata all'hub Internet.
* Lo script di installazione non è in grado di creare un'entità servizio e/o risorse di Azure.
    * Per superare questo problema, verificare che la sottoscrizione e il tenant di Azure non abbiano raggiunto i limiti di servizio massimi. Scopri di più sui [limiti e sulle restrizioni del servizio Azure ad](../../active-directory/enterprise-users/directory-service-limits-restrictions.md) e sulla [sottoscrizione di Azure e limiti, quote e vincoli](../../azure-resource-manager/management/azure-subscription-service-limits.md) dei servizi.

> [!TIP]
> Se sono presenti altri problemi che potrebbero richiedere assistenza per, **[raccogliere i log e inviare un ticket di supporto](#collect-logs-for-submitting-a-support-ticket)**. È anche possibile contattarci inviando un messaggio di posta elettronica all'indirizzo **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Analisi video in tempo reale utilizzo di moduli esterni

L'analisi di video in tempo reale tramite i processori di estensione del grafico multimediale può estendere il grafico multimediale per inviare e ricevere dati da altri moduli IoT Edge usando protocolli HTTP o gRPC. Come [esempio specifico](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension), questo grafico multimediale può inviare fotogrammi video come immagini a un modulo di inferenza esterno, ad esempio Yolo V3 e ricevere risultati analitici basati su JSON usando il protocollo http. In una topologia di questo tipo, la destinazione per gli eventi è principalmente l'hub Internet. In situazioni in cui non vengono visualizzati gli eventi di inferenza nell'hub, verificare quanto segue:

* Verificare che l'hub in cui è in corso la pubblicazione del grafo multimediale e che l'hub che si sta esaminando sia lo stesso. Quando si creano più distribuzioni, è possibile che si verifichino più hub e che si verifichi erroneamente l'hub errato per gli eventi.
* In portale di Azure verificare se il modulo esterno è distribuito e in esecuzione. Nell'immagine di esempio, rtspsim, yolov3, tinyyolov3 e logAnalyticsAgent sono IoT Edge moduli in esecuzione esterna al modulo lvaEdge.

    [![Screenshot che mostra lo stato di esecuzione dei moduli nell'hub Azure. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Verificare se si stanno inviando eventi all'endpoint URL corretto. Il contenitore esterno per intelligenza artificiale espone un URL e una porta attraverso cui riceve e restituisce i dati dalle richieste POST. Questo URL viene specificato come `endpoint: url` proprietà per il processore di estensione http. Come illustrato nell' [URL della topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json), l'endpoint viene impostato sul parametro URL di inferenza. Verificare che il valore predefinito per il parametro o il valore passato sia accurato. È possibile testare per verificare se funziona usando l'URL client (cURL).  

    Ad esempio, di seguito è riportato un contenitore Yolo V3 in esecuzione nel computer locale con un indirizzo IP di 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Risultato restituito:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Usare il **[comando Docker ispeziona](https://docs.docker.com/engine/reference/commandline/inspect/)** per trovare l'indirizzo IP del computer.
    
* Se si eseguono una o più istanze di un grafico che usa il processore di estensione media Graph, è necessario usare il `samplingOptions` campo per gestire la frequenza di fotogrammi al secondo (fps) del feed video. 

   * In alcune situazioni, in cui la CPU o la memoria del computer perimetrale è altamente utilizzata, è possibile perdere determinati eventi di inferenza. Per risolvere questo problema, impostare un valore basso per la `maximumSamplesPerSecond` proprietà nel `samplingOptions` campo. È possibile impostarlo su 0,5 ("maximumSamplesPerSecond": "0,5") in ogni istanza del grafo, quindi eseguire di nuovo l'istanza per verificare la presenza di eventi di inferenza nell'hub.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Più metodi diretti in parallelo: errore di timeout 

Analisi video in tempo reale su IoT Edge fornisce un modello di programmazione diretto basato su metodo che consente di configurare più topologie e più istanze di Graph. Nell'ambito della configurazione della topologia e del grafo, si richiamano più chiamate a metodi diretti sul modulo IoT Edge. Se si richiamano queste chiamate a più metodi in parallelo, in particolare quelle che avviano e arrestano i grafici, è possibile che si verifichi un errore di timeout simile al seguente: 

Il metodo di inizializzazione dell'assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync ha generato un'eccezione. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Si consiglia di *non* chiamare metodi diretti in parallelo. Chiamarli in modo sequenziale, ovvero effettuare una chiamata diretta al metodo solo dopo il completamento di quello precedente.

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Raccolta dei log per l'invio di un ticket di supporto

Quando i passaggi per la risoluzione dei problemi autoguidati non risolvono il problema, passare alla portale di Azure e [aprire un ticket di supporto](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> I log possono contenere informazioni personali, ad esempio l'indirizzo IP. Tutte le copie locali dei log verranno eliminate non appena viene completata l'analisi e la chiusura del ticket di supporto.  

Per raccogliere i log rilevanti che devono essere aggiunti al ticket, seguire le istruzioni riportate di seguito nell'ordine e caricare i file di log nel riquadro dei **Dettagli** della richiesta di supporto.  
1. [Configurare il modulo Live Video Analytics per raccogliere i log dettagliati](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Attiva log di debug](#live-video-analytics-debug-logs)
1. Riprodurre il problema
1. Connettersi alla macchina virtuale dalla pagina **Hub** Internet delle cose nel portale
    1. Zippare tutti i file nella cartella *debugLogs* .

       > [!NOTE]
       > Questi file di log non sono destinati alla diagnosi automatica. Sono destinati al team di progettazione di Azure per analizzare i problemi.

       * Nel comando seguente, assicurarsi di sostituire **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** con il percorso dei log di debug sul dispositivo perimetrale configurato in precedenza nel **passaggio 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Alleghi il file di *debugLogs.zip* al ticket di supporto.
1. Eseguire il [comando bundle del supporto](#use-the-support-bundle-command), raccogliere i log e connettersi al ticket di supporto.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Configurare il modulo Live Video Analytics per raccogliere i log dettagliati
Configurare il modulo Live Video Analytics per raccogliere i log dettagliati impostando `logLevel` e `logCategories` come indicato di seguito:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Questa operazione può essere eseguita in uno di questi casi:
* In **portale di Azure**, aggiornando le proprietà del modulo Identity gemelle del modulo identità del modulo di analisi video in tempo reale   [ ![ . ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* In alternativa, nel file **manifesto di distribuzione** è possibile aggiungere queste voci nel nodo Proprietà del modulo Live Video Analytics

### <a name="use-the-support-bundle-command"></a>Usare il comando support-bundle

Quando è necessario raccogliere i log da un dispositivo IoT Edge, il modo più semplice consiste nell'usare il `support-bundle` comando. Questo comando raccoglie:

- Log dei moduli
- IoT Edge Security Manager e i log del motore del contenitore
- IoT Edge controllare l'output JSON
- Informazioni di debug utili

1. Eseguire il `support-bundle` comando con il flag *--since* per specificare la quantità di tempo in cui si desidera che i log vengano coperti. Ad esempio, 2h otterrà i log per le ultime due ore. È possibile modificare il valore di questo flag per includere i registri per periodi diversi.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Questo comando crea un file denominato *support_bundle.zip* nella directory in cui è stato eseguito il comando. 
   
1. Alleghi il file di *support_bundle.zip* al ticket di supporto.

### <a name="live-video-analytics-debug-logs"></a>Log di debug di analisi video live

Per configurare l'analisi video in tempo reale sul modulo IoT Edge per generare i log di debug, effettuare le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com)e passare all'hub Internet.
1. Nel riquadro sinistro selezionare **IOT Edge**.
1. Nell'elenco dei dispositivi selezionare l'ID del dispositivo di destinazione.
1. Nella parte superiore del riquadro selezionare **imposta moduli**.

   ![Screenshot del pulsante "imposta moduli" nel portale di Azure.](media/troubleshoot-how-to/set-modules.png)

1. Nella sezione **moduli IOT Edge** cercare e selezionare **lvaEdge**.
1. Selezionare le **Opzioni di creazione del contenitore**.
1. Nella sezione **binds** aggiungere il comando seguente:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Questo comando associa le cartelle dei log tra il dispositivo perimetrale e il contenitore. Se si vogliono raccogliere i log in un percorso diverso, usare il comando seguente, sostituendo **$LOG _LOCATION_ON_EDGE_DEVICE** con il percorso che si vuole usare: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selezionare **Aggiorna**.
1. Selezionare **Rivedi e crea**. Un messaggio di convalida con esito positivo viene pubblicato sotto un banner verde.
1. Selezionare **Crea**.
1. Aggiornare l' **identità del modulo gemello** per puntare al parametro DebugLogsDirectory, che fa riferimento alla directory in cui vengono raccolti i log:

    a. Nella tabella **modules** selezionare **lvaEdge**.  
    b. Nella parte superiore del riquadro selezionare **Module Identity gemelle**. Verrà visualizzato un riquadro modificabile.  
    c. In **chiave desiderata** aggiungere la coppia chiave/valore seguente:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Questo comando associa le cartelle dei log tra il dispositivo perimetrale e il contenitore. Se si vogliono raccogliere i log in una posizione diversa nel dispositivo:
    > 1. Creare un'associazione per il percorso del log di debug nella sezione **binds** , sostituendo il **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** e **$debug _LOG_LOCATION** con il percorso desiderato: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Usare il comando seguente, sostituendo **$DEBUG _LOG_LOCATION** con il percorso usato nel passaggio precedente:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Selezionare **Salva**.


1. È possibile arrestare la raccolta dei log impostando il valore nel **modulo Identity gemelle** su *null*. Tornare alla pagina **Module Identity gemelle** e aggiornare il parametro seguente come:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Procedure consigliate per la registrazione

Il [monitoraggio e la registrazione](monitoring-logging.md) dovrebbero aiutare a comprendere la tassonomia e a generare log che consentiranno di eseguire il debug dei problemi con LVA. 

Poiché l'implementazione del server gRPC differisce tra le lingue, non esiste un modo standard per aggiungere la registrazione all'interno del server.  

Ad esempio, se si compila un server gRPC con .NET Core, il servizio gRPC aggiunge i log nella categoria **gRPC** . Per abilitare i log dettagliati da gRPC, configurare i prefissi Grpc per il livello di debug nella appsettings.jsnel file aggiungendo gli elementi seguenti alla sottosezione LogLevel della registrazione: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

È anche possibile configurarlo nel file startup. cs con ConfigureLogging: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[Registrazione e diagnostica in gRPC in .NET](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) fornisce alcune indicazioni per la raccolta di alcuni log di diagnostica da un server gRPC. 

### <a name="a-failed-grpc-connection"></a>Connessione gRPC non riuscita 

Se un grafico è attivo e in streaming da una fotocamera, la connessione verrà gestita da analisi video in tempo reale. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Monitoraggio e bilanciamento del carico delle risorse CPU e GPU quando queste risorse diventano colli di bottiglia

Analisi video live non monitora o non fornisce alcun monitoraggio delle risorse hardware. Gli sviluppatori dovranno utilizzare le soluzioni di monitoraggio dei produttori di hardware. Tuttavia, se si usano i contenitori Kubernetes, è possibile monitorare il dispositivo usando il [dashboard di Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC nei documenti .NET Core condividono anche alcune importanti informazioni sulle [procedure](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) consigliate per le prestazioni e sul [bilanciamento del carico](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Risoluzione dei problemi relativi a un server di inferenza quando non riceve frame e si riceve un errore di protocollo "sconosciuto" 

Per ottenere ulteriori informazioni sul problema, è possibile eseguire diverse operazioni.  

* Includere la categoria "**ediaPipeline** log nelle proprietà desiderate del modulo Live Video Analytics e verificare che il livello di registrazione sia impostato su `Information` .  
* Per testare la connettività di rete, è possibile eseguire il comando seguente dal dispositivo perimetrale. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Se il comando restituisce una breve stringa di testo confuso, Telnet è stato in grado di aprire una connessione al server di inferenza e aprire un canale binario gRPC. Se non viene visualizzato, Telnet segnalerà un errore di rete. 
* Nel server di inferenza è possibile abilitare la registrazione aggiuntiva nella libreria gRPC. Questo può fornire informazioni aggiuntive sul canale gRPC. Questa operazione varia in base al linguaggio. di seguito sono riportate le istruzioni per [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Selezione di più immagini dal buffer di gRPC senza restituire il risultato per il primo buffer

Come parte del contratto di trasferimento dei dati di gRPC, tutti i messaggi inviati da analisi video in tempo reale al server gRPC inferencing devono essere riconosciuti. Il mancato riconoscimento della ricezione di un frame immagine interrompe il contratto dati e può causare situazioni indesiderate.  

Per usare il server gRPC con analisi video in tempo reale, è possibile usare la memoria condivisa per ottenere prestazioni ottimali. A questo scopo è necessario usare le funzionalità di memoria condivisa di Linux esposte dal linguaggio di programmazione/ambiente. 

1. Aprire l'handle di memoria condivisa di Linux.
1. Alla ricezione di un frame, accedere all'offset dell'indirizzo all'interno della memoria condivisa.
1. Confermare il completamento dell'elaborazione del frame in modo che la memoria possa essere recuperata da analisi video in tempo reale.

   > [!NOTE]
   > Se si riducono i riconoscimenti della ricezione del frame a video live Analytics per un lungo periodo di tempo, è possibile che la memoria condivisa diventi piena e provochi le gocce di dati.
1. Archiviare ogni frame in una struttura di dati di propria scelta (elenco, matrice e così via) nel server di inferenza.
1. È quindi possibile eseguire la logica di elaborazione quando si ha il numero desiderato di fotogrammi immagine.
1. Quando si è pronti, restituire il risultato dell'inferenza a analisi video in tempo reale.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Registrazione video basata su eventi nel cloud e riproduzione dal cloud](event-based-video-recording-tutorial.md)