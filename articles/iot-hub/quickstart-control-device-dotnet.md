---
title: 'Avvio rapido: Controllare un dispositivo da hub IoT di Azure di avvio rapido (.NET) | Microsoft Docs'
description: In questa guida introduttiva vengono eseguite due applicazioni C# di esempio. Una è un'applicazione di servizio che può controllare in remoto i dispositivi connessi all'hub. L'altra applicazione simula un dispositivo connesso all'hub che può essere controllato in remoto.
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
ms.date: 03/04/2020
ms.openlocfilehash: 92f27c16b5dd45352a3a6cb65dd4bf78e88188f0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868556"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Guida introduttiva: Controllare un dispositivo connesso a un hub IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Hub IoT è un servizio di Azure che consente di gestire i dispositivi IoT dal cloud e di acquisire volumi elevati di dati di telemetria dai dispositivi nel cloud per l'archiviazione o l'elaborazione. In questa guida introduttiva viene usato un *metodo diretto* per controllare un dispositivo simulato connesso all'hub IoT. È possibile usare metodi diretti per modificare in remoto il comportamento di un dispositivo connesso all'hub IoT.

La guida introduttiva usa due applicazioni .NET già scritte.

* Un'applicazione del dispositivo simulato che risponde ai metodi diretti chiamati da un'applicazione di servizio. Per ricevere le chiamate dei metodi diretti, l'applicazione si connette a un endpoint specifico del dispositivo nell'hub IoT.

* Un'applicazione di servizio che chiama i metodi diretti sul dispositivo simulato. Per chiamare un metodo diretto su un dispositivo l'applicazione si connette a un endpoint sul lato servizio dell'hub IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Le due applicazioni di esempio eseguite in questa guida introduttiva sono scritte in C#. È necessario .NET Core SDK 3.1 o versione successiva nel computer di sviluppo.

    È possibile scaricare .NET Core SDK per più piattaforme da [.NET](https://dotnet.microsoft.com/download).

    È possibile verificare la versione corrente di C# installata nel computer di sviluppo tramite il comando seguente:

    ```cmd/sh
    dotnet --version
    ```
* Se non è già stato fatto, scaricare gli esempi di C# per Azure IoT da https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ed estrarre l'archivio ZIP.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo argomento di avvio rapido usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md), ignorare questo passaggio.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md), ignorare questo passaggio.

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyDotnetDevice**: nome del dispositivo da registrare. È consigliabile usare **MyDotnetDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, è necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

## <a name="retrieve-the-service-connection-string"></a>Recuperare la stringa di connessione del servizio

È necessaria anche una _stringa di connessione del servizio_ per l'hub IoT per consentire all'applicazione di servizio di connettersi all'hub e recuperare i messaggi. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Annotare la stringa di connessione del servizio, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Il valore verrà usato più avanti in questa guida introduttiva. Questa stringa di connessione del servizio è diversa dalla stringa di connessione del dispositivo annotata nel passaggio precedente.

## <a name="listen-for-direct-method-calls"></a>Ascoltare le chiamate dei metodi diretti

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT, invia dati di telemetria simulati e ascolta le chiamate dei metodi diretti dall'hub. In questa guida introduttiva la chiamata dei metodi diretti dall'hub indica al dispositivo di modificare l'intervallo di invio dei dati di telemetria. Il dispositivo simulato invia un acknowledgement all'hub dopo l'esecuzione del metodo diretto.

1. In una finestra del terminale locale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\SimulatedDeviceWithCommand**.

2. Nella finestra del terminale locale eseguire i comandi seguenti per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

3. Nella finestra del terminale locale eseguire il comando seguente per compilare ed eseguire l'applicazione del dispositivo simulato, sostituendo `{DeviceConnectionString}` con la stringa di connessione del dispositivo annotata in precedenza:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chiamare il metodo diretto

L'applicazione di servizio si connette a un endpoint sul lato servizio nell'hub IoT dell'utente. L'applicazione effettua chiamate dei metodi diretti a un dispositivo tramite l'hub IoT e rimane in ascolto degli acknowledgement. Un'applicazione di servizio dell'hub IoT in genere viene eseguita nel cloud.

1. In un'altra finestra del terminale locale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\InvokeDeviceMethod**.

2. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione di servizio:

    ```cmd/sh
    dotnet restore
    ```

3. Nella finestra del terminale locale eseguire i comandi seguenti per compilare ed eseguire l'applicazione di servizio, sostituendo `{ServiceConnectionString}` con la stringa di connessione del servizio annotata in precedenza:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    La schermata seguente mostra l'output visualizzato quando l'applicazione effettua una chiamata del metodo diretto al dispositivo e riceve un acknowledgement:

    ![Eseguire l'applicazione di servizio](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Dopo l'esecuzione dell'applicazione di servizio, viene visualizzato un messaggio nella finestra della console in cui è in esecuzione il dispositivo simulato e la frequenza di invio dei messaggi cambia:

    ![Modifica nel client simulato](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato chiamato un metodo diretto su un dispositivo da un'applicazione di servizio ed è stata inviata una risposta alla chiamata del metodo diretto in un'applicazione del dispositivo simulato.

Per informazioni su come indirizzare messaggi da dispositivo a cloud a diverse destinazioni nel cloud, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Esercitazione: indirizzare i dati di telemetria a endpoint diversi per l'elaborazione](tutorial-routing.md)
