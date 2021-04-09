---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 2eff30333362d461f196972fbaedbeac8f2ae7c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033860"
---
## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un'applicazione Azure IoT Central creata usando il modello **Applicazione personalizzata**. Per altre informazioni, vedere la [guida di avvio rapido per la creazione di un'applicazione](../articles/iot-central/core/quick-deploy-iot-central.md). L'applicazione deve essere stata creata a partire dal 14 luglio 2020 incluso.
* Un computer di sviluppo con [Python](https://www.python.org/) versione 3.7 o successiva installato. Per controllare la versione, è possibile eseguire `python --version` nella riga di comando. Python è disponibile per un'ampia gamma di sistemi operativi. Le istruzioni riportate in questa esercitazione presuppongono che il comando **python** venga eseguito al prompt dei comandi di Windows.
* Una copia locale del repository GitHub [Microsoft Azure IoT SDK per Python](https://github.com/Azure/azure-iot-sdk-python) che contiene il codice di esempio. Usare questo collegamento per scaricare una copia del repository: [Scarica lo ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Quindi decomprimere il file in un percorso appropriato nel computer locale.

## <a name="review-the-code"></a>Esaminare il codice

Nella copia di Microsoft Azure IoT SDK per Python scaricata in precedenza aprire il file *azure-iot-sdk-python/azure-iot-device/samples/pnp/simple_thermostat.py* in un editor di testo.

Quando si esegue l'esempio per connettersi a IoT Central, il dispositivo viene registrato tramite il servizio Device Provisioning e viene generata una stringa di connessione. L'esempio recupera le informazioni necessarie sulla connessione del servizio Device Provisioning dall'ambiente di riga di comando.

La funzione `main`:

* Usa il servizio Device Provisioning per effettuare il provisioning del dispositivo. Le informazioni sul provisioning includono l'ID modello. IoT Central usa l'ID modello per identificare o generare il modello di dispositivo per questo dispositivo. Per altre informazioni, vedere [Associare un dispositivo a un modello di dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Crea un oggetto `Device_client` e imposta l'ID modello di `dtmi:com:example:Thermostat;1` prima di aprire la connessione.
* Invia la proprietà `maxTempSinceLastReboot` a IoT Central.
* Crea un listener per il comando `getMaxMinReport`.
* Crea un listener di proprietà, per restare in ascolto degli aggiornamenti delle proprietà scrivibili.
* Avvia un ciclo per inviare dati di telemetria sulla temperatura ogni 10 secondi.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

La funzione `provision_device` usa il servizio Device Provisioning per effettuare il provisioning del dispositivo e registrarlo con IoT Central. La funzione include l'ID modello di dispositivo, usato da IoT Central per [associare un dispositivo a un modello di dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template), nel payload di provisioning:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

La funzione `execute_command_listener` gestisce le richieste del comando, esegue la funzione `max_min_handler` quando il dispositivo riceve il comando `getMaxMinReport` ed esegue la funzione `create_max_min_report_response` per generare la risposta:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener` gestisce gli aggiornamenti delle proprietà scrivibili, come `targetTemperature`, e genera la risposta JSON:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

La funzione `send_telemetry_from_thermostat` invia i messaggi con i dati di telemetria a IoT Central:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Eseguire il codice

Per eseguire l'applicazione di esempio, aprire un ambiente di riga di comando e passare alla cartella *azure-iot-sdk-python/azure-iot-device/samples/pnp* che contiene il file di esempio *simple_thermostat.js*.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Installare i pacchetti necessari:

```cmd/sh
pip install azure-iot-device
```

Eseguire l'esempio:

```cmd/sh
python simple_thermostat.py
```

L'output seguente mostra la registrazione del dispositivo e la connessione a IoT Central. L'esempio invia quindi la proprietà `maxTempSinceLastReboot` prima di iniziare a inviare dati di telemetria:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

È possibile visualizzare il modo in cui il dispositivo risponde ai comandi e agli aggiornamenti delle proprietà:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
