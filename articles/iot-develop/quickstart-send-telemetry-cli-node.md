---
title: Inviare dati di telemetria del dispositivo alla Guida introduttiva all'hub Azure Internet (Node.js)
description: In questa Guida introduttiva si usa l'SDK per dispositivi dell'hub Azure Internet per Node.js per inviare dati di telemetria da un dispositivo a un hub Internet.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197810"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub Internet (Node.js)

**Si applica a**: [sviluppo di applicazioni per dispositivi](about-iot-develop.md#device-application-development)

Questa Guida introduttiva illustra un flusso di lavoro di sviluppo di applicazioni per dispositivi di base. Si usa l'interfaccia della riga di comando di Azure per creare un hub Azure e un dispositivo simulato, quindi si usa l'SDK di Azure per la Node.js per accedere al dispositivo e inviare i dati di telemetria all'hub.

## <a name="prerequisites"></a>Prerequisiti
- Se non si dispone di sottoscrizione di Azure, [crearne una gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Interfaccia della riga di comando di Azure. È possibile eseguire tutti i comandi in questa Guida introduttiva in Azure Cloud Shell, una shell interattiva dell'interfaccia della riga di comando eseguita nel browser. Se si usa Cloud Shell, non è necessario installare alcun componente. Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa Guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire az --version per trovare la versione. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Se si usa Azure Cloud Shell, non aggiornare la versione installata di Node.js. Azure Cloud Shell include già la versione più recente di Node.js.

    Verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Usare Node.js SDK per inviare messaggi
In questa sezione si userà l'SDK Node.js per inviare messaggi dal dispositivo simulato all'hub Internet. 

1. Aprire una nuova finestra del terminale. Questo terminale verrà usato per installare il Node.js SDK e utilizzare Node.js codice di esempio. Sono ora presenti due terminali aperti: quello appena aperto per lavorare con Node.js e la shell dell'interfaccia della riga di comando usata nelle sezioni precedenti per immettere i comandi dell'interfaccia della riga di comando di Azure. 

1. Copiare gli [esempi di dispositivi Node.js SDK di Azure](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) Internet nel computer locale:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Passare alla directory *Azure-Internet e-SDK-node/Device/Samples* :

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Installare Azure Node.js SDK e le dipendenze necessarie:

    ```console
    npm install
    ```
    Questo comando installa le dipendenze appropriate come specificato nella *package.jssu* file nella directory degli esempi del dispositivo.

1. Impostare la stringa di connessione del dispositivo come variabile di ambiente denominata `DEVICE_CONNECTION_STRING` . Il valore stringa da usare è la stringa ottenuta nella sezione precedente dopo aver creato il dispositivo Node.js simulato. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Per Windows CMD non sono presenti virgolette che racchiudono la stringa di connessione.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Nella shell dell'interfaccia della riga di comando aperta, eseguire il comando [AZ Internet Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) per avviare il monitoraggio degli eventi nel dispositivo Internet delle cose.  I messaggi di evento verranno stampati nel terminale all'arrivo.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Nel terminale Node.js eseguire il codice per il file di esempio installato *simple_sample_device.js* . Questo codice consente di accedere al dispositivo dell'Internet delle cose simulate e invia un messaggio all'hub.

    Per eseguire l'esempio Node.js dal terminale:
    ```console
    node ./simple_sample_device.js
    ```

    Facoltativamente, è possibile eseguire il codice Node.js dall'esempio nell'IDE JavaScript:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Poiché il codice di Node.js Invia un messaggio di telemetria simulato dal dispositivo all'hub Internet, il messaggio viene visualizzato nella shell dell'interfaccia della riga di comando che esegue il monitoraggio degli eventi:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Il dispositivo è ora connesso in modo sicuro e invia dati di telemetria all'hub Azure.

## <a name="clean-up-resources"></a>Pulire le risorse
Se le risorse di Azure create in questa Guida introduttiva non sono più necessarie, è possibile usare l'interfaccia della riga di comando di Azure per eliminarle.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

Per eliminare un gruppo di risorse per nome:
1. Eseguire il comando [az group delete](/cli/azure/group#az-group-delete). Questo comando rimuove il gruppo di risorse, l'hub Internet e la registrazione del dispositivo creato.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Eseguire il comando [az group list](/cli/azure/group#az-group-list) per verificare che il gruppo di risorse sia stato eliminato.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato illustrato un flusso di lavoro di base dell'applicazione Azure Internet per connettere in modo sicuro un dispositivo al cloud e inviare dati di telemetria da dispositivo a cloud. È stata usata l'interfaccia della riga di comando di Azure per creare un hub e un dispositivo simulato, quindi è stato usato l'SDK di Azure per la Node.js per accedere al dispositivo e inviare i dati di telemetria all'hub. 

Come passaggio successivo, esplorare il Node.js SDK di Azure Internet Explorer tramite esempi di applicazione.

- [Altri esempi Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): questa directory contiene altri esempi del repository Node.js SDK per illustrare gli scenari dell'hub Internet.