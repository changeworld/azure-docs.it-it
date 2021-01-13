---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure (C#) | Microsoft Docs
description: In questa guida introduttiva si eseguono due applicazioni C# di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: b515274ef4bcf494c071ddb487590ff9cdccf4c0
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121482"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Avvio rapido: Inviare dati di telemetria da un dispositivo a un hub IoT e leggerli con un'applicazione di servizio (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questa guida di avvio rapido si inviano dati di telemetria da un'applicazione del dispositivo simulato, tramite l'hub IoT, a un'applicazione di servizio per l'elaborazione.

La guida introduttiva usa due applicazioni C# già pronte, una per inviare i dati di telemetria e una per leggere i dati di telemetria dall'hub. Prima di eseguire queste due applicazioni, creare un hub IoT e registrare un dispositivo con l'hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Le due applicazioni di esempio eseguite in questa guida introduttiva sono scritte in C#. È necessario .NET Core SDK 3.1 o versione successiva nel computer di sviluppo.

    È possibile scaricare .NET Core SDK per più piattaforme da [.NET](https://www.microsoft.com/net/download/all).

    È possibile verificare la versione corrente di C# installata nel computer di sviluppo tramite il comando seguente:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > È consigliabile usare .NET Core SDK 3.1 o versione successiva per la compilazione del codice del servizio Hub eventi usato per leggere i dati di telemetria in questa guida di avvio rapido.


* Scaricare gli esempi di C# per Azure IoT da [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) ed estrarre l'archivio ZIP.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo argomento di avvio rapido usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyDotnetDevice**: nome del dispositivo da registrare. È consigliabile usare **MyDotnetDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida di avvio rapido.

3. Sono necessari anche l'_endpoint compatibile con Hub eventi_, il _percorso compatibile con Hub eventi_ e la _chiave primaria di servizio_ dell'hub IoT dell'utente per consentire all'applicazione di servizio di connettersi all'hub IoT e recuperare i messaggi. I comandi seguenti recuperano questi valori per l'hub IoT:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Prendere nota di questi tre valori, che verranno usati più avanti nella guida di avvio rapido.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità.

1. In una finestra del terminale locale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\SimulatedDevice**.

2. Nella finestra del terminale locale eseguire i comandi seguenti per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

3. Nella finestra del terminale locale eseguire il comando seguente per compilare ed eseguire l'applicazione del dispositivo simulato con la stringa di connessione del dispositivo annotata in precedenza:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

L'applicazione di servizio si connette all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'applicazione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di servizio dell'hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

1. In un'altra finestra del terminale locale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\ReadD2cMessages**.

2. Nella finestra del terminale locale eseguire il comando seguente per installare le librerie necessarie per l'applicazione:

    ```cmd/sh
    dotnet restore
    ```

3. Nella finestra del terminale locale eseguire il comando seguente per visualizzare le opzioni dei parametri.

    ```cmd/sh
    dotnet run
    ```

4. Nella finestra del terminale locale eseguire uno dei comandi seguenti per compilare ed eseguire l'applicazione con:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    o

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Lo screenshot seguente mostra l'output mentre l'applicazione di servizio riceve i dati di telemetria inviati dal dispositivo simulato all'hub:

    ![Eseguire l'applicazione di servizio](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati inviati dati di telemetria simulati all'hub usando un'applicazione C# e sono stati letti i dati di telemetria dall'hub usando una semplice applicazione di servizio.

Per informazioni su come controllare il dispositivo simulato da un'applicazione di servizio, continuare con la guida di avvio rapido successiva.

> [!div class="nextstepaction"]
> [Avvio rapido: controllare un dispositivo connesso a un hub IoT](quickstart-control-device-dotnet.md)
