---
title: Inviare dati di telemetria del dispositivo alla Guida introduttiva all'hub Internet Azure (Python)
description: In questa Guida introduttiva si usa l'SDK per dispositivi dell'hub Azure Internet per Python per inviare dati di telemetria da un dispositivo a un hub Internet.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: ccf7f272069516d53732b10ec1af5bb4841f1208
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730423"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub Azure (Python)

**Si applica a**: [sviluppo di applicazioni per dispositivi](about-iot-develop.md#device-application-development)

Questa Guida introduttiva illustra un flusso di lavoro di sviluppo di applicazioni per dispositivi di base. Si usa l'interfaccia della riga di comando di Azure per creare un hub Azure e un dispositivo, quindi si usa l'SDK Python di Azure per creare un dispositivo client simulato e inviare i dati di telemetria all'hub. 

## <a name="prerequisites"></a>Prerequisiti
- Se non si dispone di sottoscrizione di Azure, [crearne una gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Interfaccia della riga di comando di Azure. È possibile eseguire tutti i comandi in questa Guida introduttiva in Azure Cloud Shell, una shell interattiva dell'interfaccia della riga di comando eseguita nel browser. Se si usa Cloud Shell, non è necessario installare alcun componente. Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa Guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire az --version per trovare la versione. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Per informazioni sulle altre versioni di Python supportate, vedere [Funzionalità per i dispositivi IoT di Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Per assicurarsi che la versione di Python sia aggiornata, eseguire `python --version` . Se sono installati sia Python 2 che Python 3 e si usa un ambiente Python 3, installare tutte le librerie usando `pip3` . In questo modo si garantisce che le librerie vengano installate nel runtime di Python 3.
    > [!IMPORTANT]
    > Nel programma di installazione di Python selezionare l'opzione per **aggiungere Python al percorso**. Se è già installato Python 3,7 o versione successiva, verificare di aver aggiunto la cartella di installazione di Python alla `PATH` variabile di ambiente.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Usare Python SDK per inviare messaggi
In questa sezione si userà Python SDK per inviare messaggi dal dispositivo simulato all'hub Internet delle cose.

1. Aprire una nuova finestra del terminale. Questo terminale verrà usato per installare Python SDK e usare il codice di esempio Python. Sono ora presenti due terminali aperti: quello appena aperto per lavorare con Python e la shell dell'interfaccia della riga di comando usata nelle sezioni precedenti per immettere i comandi dell'interfaccia della riga di comando di Azure.       

1. Copiare gli [esempi di dispositivi Python SDK per Azure Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) nel computer locale:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    e passare alla directory Azure-Internet degli *Esempi-Python/Azure-* Internet

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installare l'SDK Python per Azure:

    ```console
    pip install azure-iot-device
    ```
1. Impostare la stringa di connessione del dispositivo come variabile di ambiente denominata `IOTHUB_DEVICE_CONNECTION_STRING` . Si tratta della stringa ottenuta nella sezione precedente dopo la creazione del dispositivo Python simulato.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Per Windows CMD non sono presenti virgolette che racchiudono la stringa di connessione.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Nella shell dell'interfaccia della riga di comando aperta, eseguire il comando [AZ Internet Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub?preserve-view=true&view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) per avviare il monitoraggio degli eventi nel dispositivo Internet delle cose.  I messaggi di evento verranno stampati nel terminale all'arrivo.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Nel terminale Python eseguire il codice per il file di esempio installato *simple_send_message. py* . Questo codice consente di accedere al dispositivo dell'Internet delle cose simulate e invia un messaggio all'hub.

    Per eseguire l'esempio Python dal terminale:
    ```console
    python ./simple_send_message.py
    ```

    Facoltativamente, è possibile eseguire il codice Python dall'esempio nell'IDE di Python:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

Quando il codice Python Invia un messaggio dal dispositivo all'hub Internet, il messaggio viene visualizzato nella shell dell'interfaccia della riga di comando che esegue il monitoraggio degli eventi:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

Il dispositivo è ora connesso in modo sicuro e invia dati di telemetria all'hub Azure.

## <a name="clean-up-resources"></a>Pulire le risorse
Se le risorse di Azure create in questa Guida introduttiva non sono più necessarie, è possibile usare l'interfaccia della riga di comando di Azure per eliminarle.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate.

Per eliminare un gruppo di risorse per nome:
1. Eseguire il comando [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete). In questo modo vengono rimossi il gruppo di risorse, l'hub IoT e la registrazione del dispositivo creato.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Eseguire il comando [az group list](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-list) per verificare che il gruppo di risorse sia stato eliminato.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva è stato illustrato un flusso di lavoro di base dell'applicazione Azure Internet per connettere in modo sicuro un dispositivo al cloud e inviare dati di telemetria da dispositivo a cloud. È stata usata l'interfaccia della riga di comando di Azure per creare un hub e un dispositivo, quindi è stato usato l'SDK Python di Azure per creare un dispositivo simulato e inviare i dati di telemetria all'hub. 

Come passaggio successivo, esplorare l'SDK Python di Azure per gli esempi di applicazioni.

- [Esempi asincroni](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): questa directory contiene esempi di Python asincroni per altri scenari dell'hub Internet.
- [Esempi sincroni](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): questa directory contiene esempi di Python da usare con Python 2,7 o scenari di compatibilità sincrona per Python 3.5 +
- [Esempi di IOT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): questa directory contiene esempi di Python per l'uso con i moduli perimetrali e i dispositivi downstream.