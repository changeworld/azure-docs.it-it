---
title: 'Esercitazione: Sviluppare moduli C# per Windows con Azure IoT Edge'
description: Questa esercitazione illustra come creare moduli IoT Edge con codice C# e distribuirli in dispositivi Windows che eseguono IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 4e01b1ca9a3858ff31ad9b5da1d1159209c44330
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464063"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Esercitazione: sviluppare moduli di IoT Edge C# con i contenitori di Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Questo articolo illustra come usare Visual Studio per sviluppare codice C# e distribuirlo in un dispositivo Windows che esegue Azure IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS è il canale dell'ultimo rilascio che supporterà i contenitori di Windows. A partire dalla versione 1,2, i contenitori di Windows non sono supportati. Provare a usare o a passare a [IOT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) per eseguire IOT Edge nei dispositivi Windows.

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Usare Visual Studio per creare un modulo IoT Edge basato su C# SDK.
> * Usare Visual Studio e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo se la temperatura supera una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra come sviluppare un modulo in C# usando Visual Studio 2019 e quindi distribuirlo in un dispositivo Windows. Se si stanno sviluppando moduli con i contenitori Linux, passare a [sviluppare moduli C# IOT Edge con i contenitori Linux](tutorial-csharp-module.md) .

Per informazioni sulle opzioni per lo sviluppo e la distribuzione di moduli C# con i contenitori di Windows, vedere la tabella seguente:

| C# | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;e&nbsp;2019 |
| -- | :------------------: | :------------------: |
| Sviluppo di Windows AMD64 | ![Sviluppare moduli C# per WinAMD64 in Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Sviluppare moduli C# per WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| Debug di Windows AMD64 |   | ![Eseguire il debug di moduli C# per WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Prima di iniziare questa esercitazione, configurare l'ambiente di sviluppo seguendo le istruzioni riportate nell'esercitazione [sviluppare IOT Edge moduli con i contenitori di Windows](tutorial-develop-for-windows.md) . Al termine, l'ambiente conterrà i prerequisiti seguenti:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Windows che esegue Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Un registro contenitori, ad esempio [Registro Azure Container](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) configurato con l'estensione [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurato per eseguire i contenitori Windows.

> [!TIP]
> Se si usa Visual Studio 2017 (versione 15.7 o successiva), scaricare e installare Azure IoT Edge Tools per Visual Studio 2017 da [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Creare un progetto di modulo

In questa sezione si crea un progetto di modulo IoT Edge tramite Visual Studio e l'estensione Azure IoT Edge Tools. Dopo aver creato il modello di progetto, aggiungere nuovo codice in modo che il modulo filtri i messaggi in base alle relative proprietà segnalate.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Azure IoT Edge Tools offre modelli di progetto per tutti i linguaggi dei moduli IoT Edge supportati in Visual Studio. Questi modelli contengono tutti i file e il codice necessari per distribuire un modulo funzionante per testare IoT Edge. Possono anche fornire un punto di partenza per personalizzarli con la logica di business.

1. Aprire Visual Studio 2019 e selezionare **Crea nuovo progetto**.

1. Nel riquadro **Crea nuovo progetto** cercare **IoT Edge** e quindi, nell'elenco dei risultati, selezionare il progetto **Azure IOT Edge (Windows amd64)** .

   ![Screenshot del riquadro "Crea nuovo progetto" IoT Edge.](./media/tutorial-csharp-module-windows/new-project.png)

1. Selezionare **Avanti**.

    Viene visualizzato il riquadro **Configura il nuovo progetto**.

   ![Screenshot del riquadro "Configura il nuovo progetto".](./media/tutorial-csharp-module-windows/configure-project.png)

1. Nel riquadro **Configura il nuovo progetto** assegnare al progetto e alla soluzione un nuovo nome descrittivo, ad esempio **CSharpTutorialApp**. 

1. Selezionare **Crea** per creare il progetto.

   Viene visualizzato il riquadro **Aggiungi modulo**.

   ![Screenshot del riquadro "Aggiungi modulo" per la configurazione del progetto.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Nella pagina **Configura il nuovo progetto** procedere come segue:

   a. Nel riquadro sinistro selezionare il modello **C# Module**.  
   b. Nella casella **Nome modulo** immettere **CSharpModule**.  
   c. Nella casella **URL del repository** sostituire **localhost:5000** con il valore di **Server di accesso** del registro contenitori di Azure, nel formato seguente: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata in base al valore del nome del progetto di modulo.  È possibile recuperare il server di accesso dalla pagina di panoramica del registro contenitori nel portale di Azure.

1. Selezionare **Aggiungi** per creare il progetto.

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il manifesto della distribuzione condivide le credenziali per il registro contenitori con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. Usare le credenziali della sezione **Chiavi di accesso** del Registro Azure Container.

1. In Esplora soluzioni di Visual Studio aprire il file *deployment.template.json*.

1. Cercare la proprietà **registryCredentials** nelle proprietà desiderate di $edgeAgent. L'indirizzo del registro della proprietà dovrebbe essere compilato automaticamente con le informazioni fornite al momento della creazione del progetto. I campi nome utente e password dovrebbero contenere nomi di variabili. Ad esempio:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Aprire il file dell'ambiente (ENV) nella soluzione del modulo. Per impostazione predefinita, il file in Esplora soluzioni è nascosto, quindi per visualizzarlo può essere necessario selezionare il pulsante **Mostra tutti i file**. Il file ENV dovrebbe contenere le stesse variabili per nome utente e password riportate nel file *deployment.template.json*.

1. Aggiungere i valori di **Nome utente** e **Password** dal Registro Azure Container.

1. Salvare le modifiche al file ENV.

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

Il codice del modulo predefinito riceve i messaggi in una coda di input e li passa attraverso una coda di output. Si aggiungerà ora del codice in modo che il modulo elabori i messaggi nel dispositivo Edge prima di inoltrarli all'hub IoT. Aggiornare il modulo in modo che analizzi i dati della temperatura in ogni messaggio e invii il messaggio all'hub IoT solo se la temperatura supera una determinata soglia.

1. In Visual Studio selezionare **CSharpModule** > **Program.cs**.

1. Nella parte superiore dello spazio dei nomi **CSharpModule** aggiungere tre istruzioni **using** per i tipi che verranno usati in un secondo momento:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Aggiungere la variabile **temperatureThreshold** alla classe **Program** dopo la variabile counter. La variabile temperatureThreshold imposta il valore che la temperatura misurata deve superare perché vengano inviati dati all'hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Aggiungere le classi **MessageBody**, **Machine** e **Ambient** alla classe **Program** dopo le dichiarazioni di variabili. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

1. Cercare il metodo **Init**. Questo metodo crea e configura l'oggetto **ModuleClient**, che consente al modulo di connettersi al runtime locale di Azure IoT Edge per inviare e ricevere messaggi. Il codice registra anche un callback per la ricezione di messaggi da un hub IoT Edge tramite l'endpoint **input1**.

   Sostituire l'intero metodo Init con il codice seguente:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Questo metodo Init aggiornato configura ancora la connessione al runtime di IoT Edge con ModuleClient, ma aggiunge anche nuove funzionalità. Legge le proprietà desiderate del modulo gemello per recuperare il valore di **temperatureThreshold**. Crea quindi un callback che resta in ascolto di futuri aggiornamenti per le proprietà desiderate del modulo gemello. Con questo callback, è possibile aggiornare in remoto la soglia della temperatura nel modulo gemello e le modifiche verranno incorporate nel modulo.

   Il metodo Init aggiornato cambia anche il metodo **SetInputMessageHandlerAsync** esistente. Nell'esempio di codice i messaggi in arrivo in *input1* vengono elaborati con la funzione *PipeMessage*, ma è necessario usare invece la funzione *FilterMessages* che verrà creata nei passaggi seguenti.

1. Aggiungere un nuovo metodo **onDesiredPropertiesUpdate** alla classe **Program**. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. Rimuovere il metodo **PipeMessage** di esempio e sostituirlo con il nuovo metodo **FilterMessages**. Questo metodo viene chiamato ogni volta che l'hub di IoT Edge invia un messaggio al modulo. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello. Il metodo aggiunge anche la proprietà **MessageType** al messaggio con il valore impostato su **Avviso**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

1. Salvare il file *Program.cs*.

1. Aprire il file *deployment.template.json* nella soluzione IoT Edge. Questo file indica all'agente di IoT Edge quali moduli distribuire e indica all'hub di IoT Edge come indirizzare i messaggi tra i moduli. In questo caso i moduli da distribuire sono **SimulatedTemperatureSensor** e **CSharpModule**.

1. Aggiungere il modulo gemello **CSharpModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione `modulesContent` dopo il modulo gemello **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Screenshot che mostra il modulo gemello aggiunto al modello di distribuzione.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Salvare il file *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto codice a **CSharpModule** per filtrare i messaggi in cui la temperatura segnalata del computer è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

### <a name="sign-in-to-docker"></a>Accedere a Docker

1. Usare il comando seguente per accedere a Docker nel computer di sviluppo. Usare il nome utente, la password e il server di accesso del Registro Azure Container. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza che consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le [informazioni di riferimento sull'accesso a Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilazione ed esecuzione del push

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto da creare. Il nome predefinito è **AzureIotEdgeApp1** e poiché si sta creando un modulo Windows l'estensione dovrà essere **Windows.Amd64**.

1. Selezionare **Build and push IoT Edge modules** (Compila moduli IoT Edge ed esegui il push).

   Il comando di creazione e push avvia tre operazioni:
   * Crea prima di tutto una nuova cartella denominata *config* nella soluzione, che include il manifesto completo della distribuzione. Si basa sulle informazioni contenute nel modello di distribuzione e in altri file della soluzione. 
   * In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al Dockerfile appropriato per l'architettura di destinazione. 
   * Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

   Questo processo può richiedere diversi minuti alla prima esecuzione, ma diventa più veloce la volta successiva che si eseguono i comandi.

## <a name="deploy-modules-to-the-device"></a>Distribuire i moduli nel dispositivo

Usare Cloud Explorer di Visual Studio e l'estensione Azure IoT Edge Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Per questo scenario è già disponibile un manifesto della distribuzione, il file *deployment.windows-amd64.json* nella cartella *config*. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione.

1. In Cloud Explorer di Visual Studio espandere le risorse per visualizzare l'elenco di dispositivi IoT.

1. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge che dovrà ricevere la distribuzione.

1. Scegliere **Crea distribuzione**.

1. In Esplora file di Visual Studio selezionare il file *deployment.windows-amd64.json* nella cartella *config* della soluzione.

1. Aggiornare Cloud Explorer per visualizzare i moduli distribuiti elencati sotto il dispositivo.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Gli eventuali moduli in esecuzione nel dispositivo ma non inclusi nel manifesto della distribuzione vengono arrestati. I moduli mancanti dal dispositivo vengono avviati.

È possibile usare l'estensione IoT Edge Tools per visualizzare i messaggi in arrivo nell'hub IoT.

1. In Cloud Explorer di Visual Studio selezionare il nome del dispositivo IoT Edge.

1. Nell'elenco **Azioni** selezionare **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

1. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi potrebbe richiedere del tempo, perché il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. A causa delle modifiche apportate, il codice CSharpModule aspetta che la temperatura del computer raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**.

   ![Screenshot della finestra di output che visualizza i messaggi in arrivo nell'hub IoT.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Il modulo gemello CSharpModule è stato usato per impostare la soglia della temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio aprire il file *deployment.windows-amd64.json*. 

   *Non* aprire il file *deployment.template*. Se il manifesto della distribuzione non viene visualizzato nel file *config* di Esplora soluzioni, selezionare l'icona **Mostra tutti i file** sulla barra degli strumenti.

1. Trovare il modulo gemello CSharpModule e impostare il valore del parametro **temperatureThreshold** su una nuova temperatura da 5 a 10 gradi più alta rispetto all'ultima segnalata.

1. Salvare il file *deployment.windows-amd64.json*.

1. Seguire di nuovo le istruzioni per applicare il manifesto della distribuzione aggiornato al dispositivo.

1. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare e riutilizzare le risorse e le configurazioni create in questa esercitazione. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge.

Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati al perimetro.

> [!div class="nextstepaction"]
> [Funzioni di Azure](tutorial-deploy-function.md)
> [Analisi di flusso di Azure](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)
