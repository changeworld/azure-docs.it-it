---
title: includere file
description: includere file
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 5e3b182be067e378fa3a9354d46a9888ea4710a9
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654927"
---
Nelle sezioni seguenti si configura un terminale e si usa l'interfaccia della riga di comando di Azure per creare un hub Internet. Per configurare un terminale che esegue i comandi dell'interfaccia della riga di comando di Azure, è possibile usare la Azure Cloud Shell basata su browser o usare un terminale locale.
* Per usare Cloud Shell, passare alla sezione successiva: [avviare il cloud Shell](#launch-the-cloud-shell). 
* Per usare un terminale locale, passare alla sezione successiva e [aprire un terminale locale](#open-a-local-terminal).

## <a name="launch-the-cloud-shell"></a>Avviare Cloud Shell
In questa sezione si creerà una sessione di Cloud Shell e si configurerà l'ambiente Terminal.

Accedere al portale di Azure all'indirizzo https://portal.azure.com.  

Per avviare Cloud Shell:

1. Selezionare il pulsante **Cloud Shell** nella barra dei menu in alto a destra nel portale di Azure. 

    ![Pulsante Cloud Shell nel portale di Azure](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Se è la prima volta che si usa Cloud Shell, viene richiesto di creare una risorsa di archiviazione necessaria per usare Cloud Shell.  Selezionare una sottoscrizione per creare un account di archiviazione e una condivisione File di Microsoft Azure. 

2. Selezionare l'ambiente dell'interfaccia della riga di comando preferito nell'elenco a discesa **Seleziona ambiente**. In questa Guida introduttiva viene usato l'ambiente **Bash**. Tutti i comandi CLI seguenti funzionano anche nell'ambiente PowerShell. 

    ![Selezionare l'ambiente dell'interfaccia della riga di comando](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Ignorare la sezione successiva e passare a [installa l'estensione Azure](#install-the-azure-iot-extension)Internet. 

## <a name="open-a-local-terminal"></a>Aprire un terminale locale
Se si sceglie di usare un terminale locale anziché Cloud Shell, completare questa sezione.  

1. Aprire un terminale locale.
1. Eseguire il comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login&preserve-view=true) :

   ```azurecli
   az login
   ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo https://aka.ms/devicelogin e immettere il codice di autorizzazione visualizzato nel terminale.

    Se non è disponibile alcun browser Web o l'apertura del Web browser non riesce, usare il flusso del codice del dispositivo con `az login --use-device-code` .

1. Accedere con le credenziali dell'account nel browser.

    Per altre informazioni sui diversi metodi di autenticazione, vedere [Accedere con l'interfaccia della riga di comando di Azure]( /cli/azure/authenticate-azure-cli ).

1. Passare alla sezione successiva: [installare l'estensione Azure](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Installare l'estensione Azure Internet
In questa sezione si installa l'estensione Microsoft Azure Internet per l'interfaccia della riga di comando di Azure nella shell dell'interfaccia della riga di comando. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> I comandi del terminale nel resto di questa Guida introduttiva funzionano nello stesso Cloud Shell o in un terminale locale. Per eseguire un comando, selezionare **copia** per copiare un blocco di codice in questa Guida introduttiva. Quindi incollarlo nella shell dell'interfaccia della riga di comando ed eseguirlo.

Eseguire il comando [AZ Extension Add](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true) . 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT
In questa sezione si usa l'interfaccia della riga di comando di Azure per creare un hub e un gruppo di risorse.  Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Un hub Internet delle cose funge da Hub messaggi centrale per la comunicazione bidirezionale tra l'applicazione Internet e i dispositivi. 

Per creare un hub e un gruppo di risorse:

1. Eseguire il comando [AZ Group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true) per creare un gruppo di risorse. Il comando seguente crea un gruppo denominato *MyResourceGroup* nella posizione *eastus*. 
    >[!NOTE]
    > Facoltativamente, è possibile impostare un percorso alternativo. Per visualizzare le posizioni disponibili, eseguire `az account list-locations` . Questa esercitazione USA *eastus* come illustrato nel comando di esempio. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Eseguire il comando [az iot hub create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create&preserve-view=true) per creare un hub IoT. La creazione di un hub IoT potrebbe richiedere alcuni minuti. 

    *YourIotHubName*. Sostituire il segnaposto e le parentesi graffe circostanti nel comando seguente, usando il nome scelto per l'hub Internet delle cose. Un nome dell'hub IoT deve essere univoco a livello globale in Azure. Usare il nome dell'hub Internet nel resto di questa Guida introduttiva ogni volta che viene visualizzato il segnaposto.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Creare un dispositivo simulato
In questa sezione viene creato un dispositivo di Internet delle cose simulato connesso all'hub Internet delle cose. 

Per creare un dispositivo simulato:
1. Eseguire il comando [AZ tutto Hub Device-Identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create&preserve-view=true) nella shell dell'interfaccia della riga di comando. Verrà creata l'identità del dispositivo simulato. 

    *YourIotHubName*. sostituire il segnaposto in basso con il nome scelto per l'hub IoT. 

    *dispositivo*. È possibile usare questo nome direttamente per l'ID dispositivo simulato nella parte restante di questo articolo. Facoltativamente, usare un nome diverso. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Eseguire il comando [AZ molto Hub Device-Identity Connection-String Show](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string?view=azure-cli-latest#ext_azure_iot_az_iot_hub_device_identity_connection_string_show&preserve-view=true) . 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    L'output della stringa di connessione è nel formato seguente:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Salvare la stringa di connessione in un percorso sicuro. 

> [!NOTE]
> Mantiene aperta la shell dell'interfaccia della riga di comando. Verrà usato nei passaggi successivi.
