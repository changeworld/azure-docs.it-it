---
title: Inviare dati di telemetria del dispositivo hub IoT di Azure avvio rapido (Node.js)
description: In questa guida introduttiva si usa l'SDK hub IoT di Azure dispositivo per Node.js inviare dati di telemetria da un dispositivo a un hub Iot.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 3d42ac814678136c2f6342cd1064e3c3ff394507
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777240"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Avvio rapido: Inviare dati di telemetria da un dispositivo a un hub IoT (Node.js)

**Si applica a**: [Sviluppo di applicazioni per dispositivi](about-iot-develop.md#device-application-development)

In questa guida introduttiva si apprenderà un flusso di lavoro di sviluppo di applicazioni per dispositivi IoT di base. Usare l'interfaccia della riga di comando di Azure per creare un hub Azure IoT e un dispositivo simulato, quindi usare Azure IoT Node.js SDK per accedere al dispositivo e inviare dati di telemetria all'hub.

## <a name="prerequisites"></a>Prerequisiti
- Se non si dispone di sottoscrizione di Azure, [crearne una gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Interfaccia della riga di comando di Azure. È possibile eseguire tutti i comandi in questa Guida introduttiva in Azure Cloud Shell, una shell interattiva dell'interfaccia della riga di comando eseguita nel browser. Se si usa Cloud Shell, non è necessario installare alcun componente. Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa Guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire az --version per trovare la versione. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Se si usa Azure Cloud Shell, non aggiornare la versione installata di Node.js. Azure Cloud Shell include già la versione più recente di Node.js.

    Verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Usare l'SDK Node.js per inviare messaggi
In questa sezione si userà Node.js SDK per inviare messaggi dal dispositivo simulato all'hub IoT. 

1. Aprire una nuova finestra del terminale. Questo terminale verrà utilizzato per installare Node.js SDK e usare il Node.js di esempio. Dovrebbero ora essere aperti due terminali: quello appena aperto per usare Node.js e la shell dell'interfaccia della riga di comando usata nelle sezioni precedenti per immettere i comandi dell'interfaccia della riga di comando di Azure.

1. Copiare [Azure IoT Node.js di dispositivi SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) nel computer locale:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Passare alla directory *azure-iot-sdk-node/device/samples/pnp:*

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Installare l Azure IoT Node.js SDK e le dipendenze necessarie:

    ```console
    npm install
    ```

    Questo comando installa le dipendenze appropriate come specificato nellapackage.js *file* nella directory degli esempi di dispositivi.

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
    > Per CMD di Windows non sono presenti virgolette che circondano i valori stringa per ogni variabile.

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
1. Nella shell dell'interfaccia della riga di comando aperta eseguire [il comando az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) per avviare il monitoraggio degli eventi nel dispositivo IoT simulato.  I messaggi di evento verranno stampati nel terminale non appena arrivano.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Nel terminale Node.js eseguire il codice per il file di esempio *installatosimple_thermostat.js* . Questo codice accede al dispositivo IoT simulato e invia un messaggio all'hub IoT.

    Per eseguire lNode.js esempio seguente dal terminale:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Questo esempio di codice usa Azure IoT Plug and Play, che consente di integrare smart device nelle soluzioni senza alcuna configurazione manuale.  Per impostazione predefinita, la maggior parte degli esempi in questa documentazione usa IoT Plug and Play. Per altre informazioni sui vantaggi del PnP IoT e sui casi per usarlo o meno, vedere Che cos'è [IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Poiché il Node.js invia un messaggio di telemetria simulato dal dispositivo all'hub IoT, il messaggio viene visualizzato nella shell dell'interfaccia della riga di comando che monitora gli eventi:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
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

In questa guida introduttiva è stato appreso un flusso Azure IoT flusso di lavoro dell'applicazione per connettere in modo sicuro un dispositivo al cloud e inviare dati di telemetria da dispositivo a cloud. È stata usata l'interfaccia della riga di comando di Azure per creare un hub IoT e un dispositivo simulato, quindi è stato usato Azure IoT Node.js SDK per accedere al dispositivo e inviare dati di telemetria all'hub. 

Come passaggio successivo, esplorare l'SDK Azure IoT Node.js tramite esempi di applicazioni.

- [Altri Node.js:](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)questa directory contiene altri esempi del repository Node.js SDK per presentare gli scenari dell'hub IoT.