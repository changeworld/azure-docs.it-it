---
title: includere file
description: includere file
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 4999bd93f338ca7b34b141b88e06e4a769a4aaa1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876431"
---
Nelle sezioni seguenti si configura un terminale e si usa l'interfaccia della riga di comando di Azure per creare un hub IoT. Per configurare un terminale che esegue i comandi dell'interfaccia della riga di comando di Azure, è possibile usare il Azure Cloud Shell basato su browser o un terminale locale.
* Per usare Cloud Shell, passare alla sezione successiva: [Avviare il Cloud Shell](#launch-the-cloud-shell). 
* Per usare un terminale locale, ignorare la sezione successiva e passare [a Aprire un terminale locale.](#open-a-local-terminal)

## <a name="launch-the-cloud-shell"></a>Avviare Cloud Shell
In questa sezione viene creata una sessione di Cloud Shell e viene configurato l'ambiente terminale.

Accedere al portale di Azure all'indirizzo https://portal.azure.com.  

Per avviare Cloud Shell:

1. Selezionare il pulsante **Cloud Shell** nella barra dei menu in alto a destra nel portale di Azure. 

    ![Pulsante Cloud Shell nel portale di Azure](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Se è la prima volta che si usa Cloud Shell, viene richiesto di creare una risorsa di archiviazione necessaria per usare Cloud Shell.  Selezionare una sottoscrizione per creare un account di archiviazione e una condivisione File di Microsoft Azure. 

2. Selezionare l'ambiente dell'interfaccia della riga di comando preferito nell'elenco a discesa **Seleziona ambiente**. In questa Guida introduttiva viene usato l'ambiente **Bash**. Tutti i comandi dell'interfaccia della riga di comando seguenti funzionano anche nell'ambiente PowerShell. 

    ![Selezionare l'ambiente dell'interfaccia della riga di comando](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Ignorare la sezione successiva e passare a [Installare l'estensione Azure IoT .](#install-the-azure-iot-extension) 

## <a name="open-a-local-terminal"></a>Aprire un terminale locale
Se si è scelto di usare un terminale locale anziché Cloud Shell, completare questa sezione.  

1. Aprire un terminale locale.
1. Eseguire il [comando az login:](/cli/azure/reference-index#az_login)

   ```azurecli
   az login
   ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo https://aka.ms/devicelogin e immettere il codice di autorizzazione visualizzato nel terminale.

    Se non è disponibile alcun Web browser o il Web browser non si apre, usare il flusso del codice del dispositivo con `az login --use-device-code` .

1. Accedere con le credenziali dell'account nel browser.

    Per altre informazioni sui diversi metodi di autenticazione, vedere [Accedere con l'interfaccia della riga di comando di Azure]( /cli/azure/authenticate-azure-cli ).

1. Passare alla sezione successiva: [Installare l'estensione Azure IoT .](#install-the-azure-iot-extension) 

## <a name="install-the-azure-iot-extension"></a>Installare l'estensione Azure IoT
In questa sezione viene installata l'estensione IoT Microsoft Azure per l'interfaccia della riga di comando di Azure nella shell dell'interfaccia della riga di comando. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> I comandi del terminale nella parte restante di questa guida introduttiva funzionano allo stesso modo in Cloud Shell o in un terminale locale. Per eseguire un comando, selezionare **Copia per** copiare un blocco di codice in questa guida introduttiva. Incollarlo quindi nella shell dell'interfaccia della riga di comando ed eseguirlo.

Eseguire il [comando az extension add.](/cli/azure/extension#az_extension_add) 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT
In questa sezione si usa l'interfaccia della riga di comando di Azure per creare un hub IoT e un gruppo di risorse.  Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Un hub IoT funge da hub messaggi centrale per la comunicazione bidirezionale tra l'applicazione IoT e i dispositivi. 

Per creare un hub IoT e un gruppo di risorse:

1. Eseguire il [comando az group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse. Il comando seguente crea un gruppo denominato *MyResourceGroup* nella posizione *eastus*. 
    >[!NOTE]
    > Facoltativamente, è possibile impostare una posizione alternativa. Per visualizzare i percorsi disponibili, eseguire `az account list-locations` . Questa esercitazione usa *eastus* come illustrato nel comando di esempio. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Eseguire il comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) per creare un hub IoT. La creazione di un hub IoT potrebbe richiedere alcuni minuti. 

    *YourIotHubName*. Sostituire questo segnaposto e le parentesi graffe circostanti nel comando seguente, usando il nome scelto per l'hub IoT. Un nome dell'hub IoT deve essere univoco a livello globale in Azure. Usare il nome dell'hub IoT nella parte restante di questa guida introduttiva ovunque sia visualizzato il segnaposto.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Creare un dispositivo simulato
In questa sezione viene creato un dispositivo IoT simulato connesso all'hub IoT. 

Per creare un dispositivo simulato:
1. Eseguire il [comando az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) nella shell dell'interfaccia della riga di comando. Verrà creata l'identità del dispositivo simulato. 

    *YourIotHubName*. sostituire il segnaposto in basso con il nome scelto per l'hub IoT. 

    *myDevice*. È possibile usare questo nome direttamente per l'ID dispositivo simulato nel resto di questo articolo. Facoltativamente, usare un nome diverso. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Eseguire il [comando az iot hub device-identity connection-string show.](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    L'output della stringa di connessione è nel formato seguente:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Salvare la stringa di connessione in un percorso sicuro. 

> [!NOTE]
> Mantenere aperta la shell dell'interfaccia della riga di comando. Verrà usato nei passaggi successivi.