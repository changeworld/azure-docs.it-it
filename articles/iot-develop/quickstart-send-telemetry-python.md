---
title: Inviare dati di telemetria del dispositivo alla Guida introduttiva di Azure IoT Central (Python)
description: In questa Guida introduttiva si usa l'SDK per dispositivi dell'hub Azure Internet per Python per inviare dati di telemetria da un dispositivo a IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d0dcca0c4be801f385a48afcd41b6a547bab3fbe
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654895"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Guida introduttiva: inviare dati di telemetria da un dispositivo ad Azure IoT Central (Python)

**Si applica a**: [sviluppo di applicazioni per dispositivi](about-iot-develop.md#device-application-development)

Questa Guida introduttiva illustra un flusso di lavoro di sviluppo di applicazioni per dispositivi di base. Usare prima di tutto Azure IoT Central per creare un'applicazione cloud. Si usa quindi l'SDK Python di Azure per creare un dispositivo simulato, connettersi a IoT Central e inviare dati di telemetria da dispositivo a cloud. 

## <a name="prerequisites"></a>Prerequisiti
- [Python 3.7+](https://www.python.org/downloads/). Per informazioni sulle altre versioni di Python supportate, vedere [Funzionalità per i dispositivi IoT di Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Per assicurarsi che la versione di Python sia aggiornata, eseguire `python --version` . Se sono installati sia Python 2 che Python 3 e si usa un ambiente Python 3, installare tutte le librerie usando `pip3` . `pip3`L'esecuzione garantisce che le librerie vengano installate nel runtime di Python 3.
    > [!IMPORTANT]
    > Nel programma di installazione di Python selezionare l'opzione per **aggiungere Python al percorso**. Se è già installato Python 3,7 o versione successiva, verificare di aver aggiunto la cartella di installazione di Python alla `PATH` variabile di ambiente.

## <a name="create-an-application"></a>Creare un'applicazione
In questa sezione si creerà un'applicazione IoT Central. IoT Central è una piattaforma applicativa basata su portale che consente di ridurre la complessità e i costi di sviluppo e gestione delle soluzioni Internet delle cose.

Per creare un'applicazione IoT Central di Azure:
1. Passare ad [Azure IOT Central](https://apps.azureiotcentral.com/) e accedere con un account Microsoft personale, aziendale o dell'Istituto di istruzione.
1. Passare a **Compila** e selezionare **app personalizzate**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Pagina iniziale IoT Central":::
1. In **nome applicazione** immettere un nome univoco o utilizzare il nome generato.
1. In **URL** immettere un prefisso URL applicazione memorabile o usare il prefisso URL generato.
1. Lasciare il **modello di applicazione** impostato su *applicazione personalizzata*. L'elenco a discesa potrebbe visualizzare altre opzioni, se sono già presenti modelli nell'account.
1. Selezionare un'opzione per il **piano tariffario** . 
    - Per usare gratuitamente l'applicazione per sette giorni, selezionare **gratuito**. È possibile convertire un'applicazione gratuita in prezzi standard prima della scadenza.
    - Facoltativamente, è possibile selezionare un piano tariffario standard. Se si selezionano i prezzi standard, vengono visualizzate più opzioni ed è necessario impostare una **directory**, una **sottoscrizione di Azure** e una **località**. Per informazioni sui prezzi, vedere [prezzi di Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - La **directory** è l'istanza di Azure Active Directory in cui creare l'applicazione. Un'istanza di Azure Active Directory contiene le identità degli utenti, le credenziali e altre informazioni dell'organizzazione. Se non si dispone di un Azure Active Directory, ne viene creato uno quando si crea una sottoscrizione di Azure.
        - Una **sottoscrizione di Azure** consente di creare istanze di servizi di Azure. IoT Central effettua il provisioning delle risorse nella sottoscrizione. Se non si ha una sottoscrizione di Azure, è possibile [crearne una gratuitamente](https://azure.microsoft.com/free/). Dopo aver creato la sottoscrizione, tornare alla pagina IoT Central **nuova applicazione** . La nuova sottoscrizione verrà visualizzata nell'elenco a discesa **sottoscrizione Azure** .
        - **Location** è la [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si crea un'applicazione. Selezionare un percorso fisicamente più vicino ai dispositivi per ottenere prestazioni ottimali. Dopo aver scelto un percorso, non è possibile spostare l'applicazione in un percorso diverso.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="IoT Central finestra di dialogo nuova applicazione":::
1. Selezionare **Crea**.
    
    Dopo la creazione dell'applicazione, il IoT Central viene reindirizzato al dashboard dell'applicazione.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central nuovo dashboard dell'applicazione":::

## <a name="add-a-device"></a>Aggiungere un dispositivo
In questa sezione si aggiungerà un nuovo dispositivo all'applicazione IoT Central. Il dispositivo è un'istanza di un modello di dispositivo che rappresenta un dispositivo reale o simulato che verrà connesso all'applicazione. 

Per creare un nuovo dispositivo:
1. Nel riquadro sinistro selezionare **dispositivi**, quindi selezionare **+ nuovo**. Verrà visualizzata la finestra di dialogo nuovo dispositivo.
1. Lasciare il **modello di dispositivo** impostato su non *assegnato*.

    > [!NOTE]
    > Per semplicità, in questa Guida introduttiva si connette un dispositivo simulato che usa un modello non assegnato. Se si continua a usare IoT Central per gestire i dispositivi, si apprenderà come usare i modelli di dispositivo. Per una panoramica dell'uso dei modelli di dispositivo, vedere [Guida introduttiva: aggiungere un dispositivo simulato all'applicazione IoT Central](../iot-central/core/quick-create-simulated-device.md).
1. Impostare un **nome** descrittivo e un **ID dispositivo**. Facoltativamente, utilizzare i valori generati.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Finestra di dialogo IoT Central nuovo dispositivo":::
1. Selezionare **Crea**.

    Il dispositivo creato verrà visualizzato nell'elenco **tutti i dispositivi** .
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Elenco IoT Central tutti i dispositivi":::
    
Per recuperare i dettagli della connessione per il nuovo dispositivo:
1. Nell'elenco **tutti i dispositivi** fare doppio clic sul nome del dispositivo collegato per visualizzare i dettagli. 
1. Nel menu superiore selezionare **Connetti**.

    Nella finestra di dialogo **connessione dispositivo** vengono visualizzati i dettagli della connessione:  :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IOT Central dettagli connessione dispositivo":::
1. Copiare i valori seguenti dalla finestra di dialogo di **connessione del dispositivo** a una posizione sicura. Questi verranno usati nella sezione successiva per connettere il dispositivo a IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Inviare messaggi e monitorare i dati di telemetria
In questa sezione si userà Python SDK per creare un dispositivo simulato e inviare i dati di telemetria all'applicazione IoT Central. 

1. Aprire un terminale usando CMD di Windows o PowerShell o bash (per Windows o Linux). Si userà il terminale per installare Python SDK, aggiornare le variabili di ambiente ed eseguire l'esempio di codice Python.

1. Copiare gli [esempi di dispositivi Python SDK per Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) , nel computer locale.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Passare alla directory Azure-Internet e- *SDK-Python/Azure-* Internet.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installare l'SDK Python per Azure.

    ```console
    pip install azure-iot-device
    ```

1. Impostare ognuna delle variabili di ambiente seguenti per consentire al dispositivo simulato di connettersi a IoT Central. Per `ID_SCOPE` , `DEVICE_ID` e `DEVICE_KEY` , usare i valori salvati dalla finestra di dialogo IOT Central *connessione del dispositivo* .

    **CMD di Windows**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Per Windows CMD non sono presenti virgolette che racchiudono la stringa di connessione o altri valori di variabile.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux o Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. Nel terminale eseguire il codice per il file di esempio * simple_send_temperature. py. Questo codice consente di accedere al dispositivo dell'Internet delle cose simulate e invia un messaggio a IoT Central.

    Per eseguire l'esempio Python dal terminale:
    ```console
    python ./simple_send_temperature.py
    ```

    Facoltativamente, è possibile eseguire il codice Python dall'esempio nell'IDE di Python:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Quando il codice Python Invia un messaggio dal dispositivo all'applicazione IoT Central, i messaggi vengono visualizzati nella scheda **dati non elaborati** del dispositivo in IOT Central. Potrebbe essere necessario aggiornare la pagina per visualizzare i messaggi recenti.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Screenshot dell'output di IoT Central dati non elaborati":::

Il dispositivo è ora connesso in modo sicuro e invia dati di telemetria ad Azure.

## <a name="clean-up-resources"></a>Pulire le risorse
Se le risorse IoT Central create in questa esercitazione non sono più necessarie, è possibile eliminarle dal portale di IoT Central. Facoltativamente, se si prevede di continuare seguendo la documentazione in questa guida, è possibile mantenere l'applicazione creata e riutilizzarla per altri esempi.

Per rimuovere l'applicazione di esempio Azure IoT Central e tutti i relativi dispositivi e risorse:
1. Selezionare **Amministrazione**  >  **dell'applicazione**.
1. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato illustrato un flusso di lavoro di base dell'applicazione Azure Internet per connettere in modo sicuro un dispositivo al cloud e inviare dati di telemetria da dispositivo a cloud. È stato usato il IoT Central di Azure per creare un'applicazione e un dispositivo, quindi è stato usato Azure Internet Azure per Python SDK per creare un dispositivo simulato e inviare dati di telemetria. È stato anche usato IoT Central per monitorare i dati di telemetria.

Come passaggio successivo, esplorare l'SDK Python di Azure per gli esempi di applicazioni.

- [Esempi asincroni](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): questa directory contiene esempi di Python asincroni per altri scenari dell'hub Internet.
- [Esempi sincroni](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): questa directory contiene esempi di Python da usare con Python 2,7 o scenari di compatibilità sincrona per Python 3.5 +
- [Esempi di IOT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): questa directory contiene esempi di Python per l'uso con i moduli perimetrali e i dispositivi downstream.
