---
title: 'Avvio rapido: Inviare dati di telemetria hub IoT di Azure dispositivo a un dispositivo (Python)'
description: In questa guida introduttiva si usa l'SDK per dispositivi hub IoT di Azure per Python per inviare dati di telemetria da un dispositivo a un hub IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: be44ecf6dd154a21fd024ee37d92f61504350664
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876432"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub Azure IoT (Python)

**Si applica a:** [Sviluppo di applicazioni per dispositivi](about-iot-develop.md#device-application-development)

In questa guida introduttiva si apprenderà un flusso di lavoro di base per lo sviluppo di applicazioni per dispositivi IoT. Si usa l'interfaccia della riga di comando di Azure per creare un hub Azure IoT e un dispositivo, quindi si usa Azure IoT Python SDK per creare un dispositivo client simulato e inviare dati di telemetria all'hub. 

## <a name="prerequisites"></a>Prerequisiti
- Se non si dispone di sottoscrizione di Azure, [crearne una gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Interfaccia della riga di comando di Azure. È possibile eseguire tutti i comandi in questa Guida introduttiva in Azure Cloud Shell, una shell interattiva dell'interfaccia della riga di comando eseguita nel browser. Se si usa Cloud Shell, non è necessario installare alcun componente. Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa Guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire az --version per trovare la versione. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Per informazioni sulle altre versioni di Python supportate, vedere [Funzionalità per i dispositivi IoT di Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Per assicurarsi che la versione di Python sia aggiornata, eseguire `python --version` . Se sono installati sia Python 2 che Python 3 e si usa un ambiente Python 3, installare tutte le librerie usando `pip3` . Questo comando garantisce che le librerie siano installate nel runtime di Python 3.
    > [!IMPORTANT]
    > Nel programma di installazione di Python selezionare l'opzione **Add Python to PATH (Aggiungi Python a PATH).** Se Python 3.7 o versione successiva è già installato, verificare di aver aggiunto la cartella di installazione di Python alla variabile `PATH` di ambiente .

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Usare Python SDK per inviare messaggi
In questa sezione si userà Python SDK per inviare messaggi dal dispositivo simulato all'hub IoT.

1. Aprire una nuova finestra del terminale. Questo terminale verrà utilizzato per installare Python SDK e usare il codice di esempio Python. A questo punto dovrebbero essere aperti due terminali: quello appena aperto per lavorare con Python e la shell dell'interfaccia della riga di comando usata nelle sezioni precedenti per immettere i comandi dell'interfaccia della riga di comando di Azure.       

1. Copiare [gli esempi Azure IoT dispositivo Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) nel computer locale:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Passare alla directory *azure-iot-sdk-python/azure-iot-device/samples/pnp:*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Installare l'Azure IoT Python SDK:

    ```console
    pip install azure-iot-device
    ```
1. Impostare entrambe le variabili di ambiente seguenti per consentire al dispositivo simulato di connettersi Azure IoT.
    * Impostare una variabile di ambiente denominata `IOTHUB_DEVICE_CONNECTION_STRING` . Per il valore della variabile, usare la stringa di connessione del dispositivo salvata nella sezione precedente.
    * Impostare una variabile di ambiente denominata `IOTHUB_DEVICE_SECURITY_TYPE` . Per la variabile usare il valore stringa letterale `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Per Cmd di Windows non sono presenti virgolette che circondano i valori stringa per ogni variabile.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux o Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. Nella shell dell'interfaccia della riga di comando aperta eseguire [il comando az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) per avviare il monitoraggio degli eventi nel dispositivo IoT simulato.  I messaggi di evento vengono stampati nel terminale all'arrivo.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Nel terminale Python eseguire il codice per il file di esempio *installato simple_thermostat.py*. Questo codice accede al dispositivo IoT simulato e invia un messaggio all'hub IoT.

    Per eseguire l'esempio Python dal terminale:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > Questo esempio di codice usa Azure IoT Plug and Play, che consente di integrare i dispositivi intelligenti nelle soluzioni senza alcuna configurazione manuale.  Per impostazione predefinita, la maggior parte degli esempi in questa documentazione usa IoT Plug and Play. Per altre informazioni sui vantaggi di IoT PnP e sui casi in cui è possibile usarlo o meno, vedere Che cos'è [IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

 Quando il codice Python invia un messaggio dal dispositivo all'hub IoT, il messaggio viene visualizzato nella shell dell'interfaccia della riga di comando che monitora gli eventi:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

Il dispositivo è ora connesso in modo sicuro e invia dati di telemetria hub IoT di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse
Se le risorse di Azure create in questa Guida introduttiva non sono più necessarie, è possibile usare l'interfaccia della riga di comando di Azure per eliminarle.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate.

Per eliminare un gruppo di risorse per nome:
1. Eseguire il comando [az group delete](/cli/azure/group#az_group_delete). Questo comando rimuove il gruppo di risorse, l'hub IoT e la registrazione del dispositivo creata.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Eseguire il comando [az group list](/cli/azure/group#az_group_list) per verificare che il gruppo di risorse sia stato eliminato.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato appreso un flusso di Azure IoT di applicazioni di base per connettere in modo sicuro un dispositivo al cloud e inviare dati di telemetria da dispositivo a cloud. È stata usata l'interfaccia della riga di comando di Azure per creare un hub IoT e un dispositivo, quindi è stato usato Azure IoT Python SDK per compilare un dispositivo simulato e inviare dati di telemetria all'hub. 

Come passaggio successivo, esplorare l'sdk Azure IoT Python tramite esempi di applicazioni.

- [Esempi asincroni:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)questa directory contiene esempi python asincroni per altri scenari dell'hub IoT.
- [Esempi sincroni: questa](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)directory contiene esempi python da usare con Python 2.7 o scenari di compatibilità sincrona per Python 3.6+
- [IoT Edge: questa](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)directory contiene esempi Python per l'uso di moduli Edge e dispositivi downstream.
