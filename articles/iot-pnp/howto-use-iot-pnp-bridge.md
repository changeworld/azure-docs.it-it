---
title: Come connettere un esempio Plug and Play Bridge in esecuzione su Linux o Windows a un hub Internet. Microsoft Docs
description: Consente di compilare ed eseguire l'Plug and Play Bridge in Linux o Windows che si connette a un hub Internet. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28feaf266e2d5bed14077a686e6eb936e430a695
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066756"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Come connettere un esempio Plug and Play Bridge in esecuzione in Linux o Windows all'hub Internet

Questo articolo illustra come creare la scheda dell'ambiente di esempio Plug and Play del Bridge, connetterla all'hub Internet e usare lo strumento Azure Internet Explorer per visualizzare i dati di telemetria inviati. Il Bridge Plug and Play è scritto in C e include l'SDK per dispositivi Azure per dispositivi per C. Al termine di questa esercitazione, si dovrebbe essere in grado di eseguire il Plug and Play Bridge e visualizzare i dati di telemetria del report in Azure Internet Explorer:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Screenshot che mostra Azure Internet Explorer con una tabella di dati di telemetria segnalati (umidità, temperatura) del Plug and Play Bridge.":::

## <a name="prerequisites"></a>Prerequisiti

È possibile eseguire l'esempio nell'articolo su Windows o Linux. I comandi della shell in questa guida alle procedure seguono la convenzione di Windows per i separatori di percorso ' `\` '. Se si segue in Linux, assicurarsi di scambiare questi separatori per ' `/` '.

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questo articolo, è possibile usare lo strumento **Azure Internet Explorer** . [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub Internet_ per l'hub. Prendere nota di questa stringa di connessione, che verrà usata più avanti in questo articolo:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota di questa stringa di connessione, che verrà usata più avanti in questo articolo:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Scaricare ed eseguire il Bridge

In questo articolo sono disponibili due opzioni per eseguire il Bridge. È possibile:

- Scaricare un file eseguibile predefinito ed eseguirlo come descritto in questa sezione.
- Scaricare il codice sorgente e quindi [compilare ed eseguire il Bridge](#build-and-run-the-bridge) come descritto nella sezione seguente.

Per scaricare ed eseguire il Bridge:

1. Passare alla [pagina delle versioni](https://github.com/Azure/iot-plug-and-play-bridge/releases)plug and Play di Internet delle cose.
1. Scaricare l'eseguibile predefinito per il sistema operativo in uso: **pnpbridge_bin.exe** per Windows o **pnpbridge_bin** per Linux.
1. Scaricare ilconfig.jsdi esempio [ nel](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) file di configurazione per l'esempio di sensore ambientale. Verificare che il file di configurazione si trovi nella stessa cartella del file eseguibile.
1. Modificare il *config.jsnel* file:

    - Aggiungere il `connection-string` valore che è la _stringa di connessione del dispositivo_ di cui si è preso nota in precedenza.
    - Aggiungere il `symmetric_key` valore della chiave di accesso condiviso dalla stringa di _connessione del dispositivo_.
    - Sostituire il `root_interface_model_id` valore con `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    La prima sezione del *config.jsnel* file è ora simile al frammento di codice seguente:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Eseguire il file eseguibile nell'ambiente della riga di comando. Il Bridge genera un output simile al seguente:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Compilare ed eseguire il Bridge

Se si preferisce creare manualmente il file eseguibile, è possibile scaricare il codice sorgente e gli script di compilazione.

Aprire un prompt dei comandi in una cartella di propria scelta. Eseguire il comando seguente per clonare il repository GitHub [plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) in questa posizione:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Dopo aver clonato il repository, aggiornare i moduli secondari. I moduli secondari includono Azure Internet per l'SDK per C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Il completamento di questa operazione richiederà alcuni minuti.

> [!TIP]
> Se si verificano problemi con l'aggiornamento del modulo secondario del clone git, si tratta di un problema noto relativo ai percorsi dei file di Windows. Per risolvere il problema, è possibile provare il comando seguente: `git config --system core.longpaths true`

I prerequisiti per la compilazione del Bridge variano in base al sistema operativo:

### <a name="windows"></a>Windows

Per compilare il Plug and Play Bridge in Windows, installare il software seguente:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro **Sviluppo di applicazioni desktop con C++** quando si [installa](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="linux"></a>Linux

Questo articolo presuppone che si stia usando Ubuntu Linux. I passaggi descritti in questo articolo sono stati testati con Ubuntu 18,04.

Per compilare il Plug and Play Bridge in Linux, installare **GCC**, **git**, **CMake** e tutte le dipendenze necessarie usando il `apt-get` comando:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verificare che la versione di `cmake` sia superiore alla **2.8.12** e che la versione di **GCC** sia superiore alla **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Crea il Bridge Plug and Play

Passare alla cartella *pnpbridge* nella directory del repository.

Per Windows eseguire quanto segue in una [prompt dei comandi per gli sviluppatori per Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Analogamente per Linux eseguire quanto segue:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>In Windows è possibile aprire la soluzione generata dal comando cmake in Visual Studio 2019. Aprire il file di progetto *azure_iot_pnp_bridge. sln* nella directory CMake e impostare il progetto *pnpbridge_bin* come progetto di avvio nella soluzione. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

### <a name="edit-the-configuration-file"></a>Modificare il file di configurazione

Per ulteriori informazioni sui file di configurazione, vedere il documento relativo ai [concetti relativi a plug and Play Bridge](concepts-iot-pnp-bridge.md).

Aprire il *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.js* nel file in un editor di testo.

- Aggiungere il `connection-string` valore che è la _stringa di connessione del dispositivo_ di cui si è preso nota in precedenza.
- Aggiungere il `symmetric_key` valore della chiave di accesso condiviso dalla stringa di _connessione del dispositivo_.
- Sostituire il `root_interface_model_id` valore con `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

La prima sezione del *config.jsnel* file è ora simile al frammento di codice seguente:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Esegui il Bridge Plug and Play

Avviare l'esempio del sensore di ambiente Internet delle cose Plug and Play Bridge. Il parametro è il percorso del `config.json` file modificato nella sezione precedente:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Il Bridge genera un output simile al seguente:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Usare i comandi seguenti per eseguire il Bridge in Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Scaricare i file del modello

Usare Azure Internet Explorer in un secondo momento per visualizzare il dispositivo quando si connette all'hub Internet. Azure Internet Explorer richiede una copia locale del file del modello corrispondente all' **ID modello** inviato dal dispositivo. Il file del modello consente a Internet Explorer di visualizzare i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Per scaricare i modelli per Azure Internet Explorer:

1. Nel computer locale creare una cartella denominata *models*.
1. Salvare [EnvironmentalSensor.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) nella cartella *Models* creata nel passaggio precedente.
1. Se si apre il file del modello in un editor di testo, è possibile visualizzare il modello che definisce un componente con `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` come ID. Si tratta dello stesso ID modello usato nella *config.jssu* file.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio del Bridge, usare lo strumento Azure Internet Explorer per verificarne il funzionamento. È possibile visualizzare i dati di telemetria, le proprietà e i comandi definiti nel `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` modello.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come connettere un Plug and Play del dispositivo a un hub. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

* [Informazioni sul Bridge Plug and Play](./concepts-iot-pnp-bridge.md)
* [Crea, Distribuisci ed Estendi Plug and Play Bridge](howto-build-deploy-extend-pnp-bridge.md)
* [Plug and Play Bridge su GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
