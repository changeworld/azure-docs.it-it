---
title: Connettere un gateway trasparente IoT Edge a un'applicazione Azure IoT Central
description: Come connettere i dispositivi tramite un gateway trasparente IoT Edge a un'applicazione IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bdfb5f65106f3f8843b4aa52b752f5e563ab03f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620047"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Come connettere i dispositivi tramite un gateway trasparente IoT Edge

Un dispositivo IoT Edge può fungere da gateway che fornisce una connessione tra altri dispositivi in una rete locale e l'applicazione IoT Central. Si usa un gateway quando il dispositivo non è in grado di accedere direttamente all'applicazione IoT Central.

IoT Edge supporta i [modelli di gateway *Transparent* e *Translation*](../../iot-edge/iot-edge-as-gateway.md). Questo articolo riepiloga come implementare il modello di gateway trasparente. In questo modello, il gateway passa i messaggi dal dispositivo downstream attraverso l'endpoint dell'hub Internet all'interno dell'applicazione IoT Central.

Questo articolo usa le macchine virtuali per ospitare il dispositivo e il gateway downstream. In uno scenario reale, il dispositivo e il gateway downstream vengono eseguiti su dispositivi fisici nella rete locale.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi dell'esercitazione, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Completare la guida di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) per creare un'applicazione IoT Central usando il modello **App personalizzata > Applicazione personalizzata**.

Per seguire la procedura descritta in questo articolo, scaricare i file seguenti nel computer:

- [Modello del dispositivo termostato](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Manifesto del gateway trasparente](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Aggiungere modelli di dispositivo

Sia i dispositivi downstream che il dispositivo gateway richiedono modelli di dispositivo in IoT Central. IoT Central consente di modellare la relazione tra i dispositivi downstream e il gateway in modo che sia possibile visualizzarli e gestirli dopo essersi connessi.

Per creare un modello di dispositivo per un dispositivo downstream, creare un modello di dispositivo standard che modella le funzionalità del dispositivo. L'esempio illustrato in questo articolo usa il modello del dispositivo termostato.

Per creare un modello di dispositivo per un dispositivo downstream:

1. Creare un modello di dispositivo e scegliere il **dispositivo** Internet come tipo di modello.

1. Nella pagina **personalizzazione** della procedura guidata immettere un nome, ad esempio *termostato* , per il modello di dispositivo.

1. Dopo aver creato il modello di dispositivo, selezionare **Importa un modello**. Selezionare un modello, ad esempio il *thermostat-1.jsnel* file scaricato in precedenza.

1. Per generare alcune visualizzazioni predefinite per il termostato, selezionare Visualizzazioni, quindi scegliere **Genera visualizzazioni predefinite**.

1. Pubblicare il modello di dispositivo.

Per creare un modello di dispositivo per un gateway IoT Edge trasparente:

1. Creare un modello di dispositivo e scegliere **Azure IOT Edge** come tipo di modello.

1. Nella pagina **personalizzazione** della procedura guidata immettere un nome, ad esempio *gateway perimetrale* per il modello di dispositivo.

1. Nella pagina **personalizzazione** della procedura guidata, controllare il **dispositivo gateway con i dispositivi downstream**.

1. Nella pagina **personalizzazione** della procedura guidata selezionare **Sfoglia**. Caricare il *EdgeTransparentGatewayManifest.jsnel* file scaricato in precedenza.

1. Aggiungere una voce nelle **relazioni** al modello di dispositivo downstream.

Lo screenshot seguente mostra la pagina delle **relazioni** per un dispositivo gateway IOT Edge con dispositivi downstream che usano il modello di dispositivo **termostato** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Screenshot che illustra IoT Edge relazione del modello di dispositivo gateway con un modello di dispositivo a valle del termostato.":::

La schermata precedente mostra un modello di dispositivo gateway IoT Edge senza moduli definiti. Un gateway trasparente non richiede alcun modulo perché il runtime di IoT Edge invia i messaggi dai dispositivi downstream ai IoT Central. Se il gateway deve inviare dati di telemetria, sincronizzare le proprietà o gestire i comandi, è possibile definire queste funzionalità nel componente predefinito o in un modulo.

Aggiungere le proprietà e le viste del cloud richieste prima di pubblicare il gateway e i modelli di dispositivo downstream.

## <a name="add-the-devices"></a>Aggiungere i dispositivi

Quando si aggiungono i dispositivi all'applicazione IoT Central, è possibile definire la relazione tra i dispositivi downstream e il gateway trasparente.

Per aggiungere i dispositivi:

1. Passare alla pagina dispositivi nell'applicazione IoT Central.

1. Aggiungere un'istanza del gateway trasparente IoT Edge dispositivo. In questo articolo l'ID del dispositivo gateway è `edgegateway` .

1. Aggiungere una o più istanze del dispositivo downstream. In questo articolo i dispositivi downstream sono termostati con ID `thermostat1` e `thermostat2` .

1. Nell'elenco dispositivo selezionare ogni dispositivo downstream e selezionare **Connetti al gateway**.

La schermata seguente mostra che è possibile visualizzare l'elenco dei dispositivi collegati a un gateway nella pagina **dispositivi downstream** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Screenshot che mostra l'elenco di dispositivi downstream connessi a un gateway trasparente.":::

In un gateway trasparente, i dispositivi downstream si connettono al gateway stesso, non a un modulo personalizzato ospitato dal gateway.

Prima di distribuire i dispositivi, è necessario:

- **Ambito ID** dell'applicazione IoT Central.
- Valori **ID dispositivo** per il gateway e i dispositivi downstream.
- Valori di **chiave primaria** per il gateway e i dispositivi downstream.

Per trovare questi valori, passare a ogni dispositivo nell'elenco dei dispositivi e selezionare **Connetti**. Prendere nota di questi valori prima di continuare.

## <a name="deploy-the-gateway-and-devices"></a>Distribuire il gateway e i dispositivi

Per consentire all'utente di provare questo scenario, i passaggi seguenti illustrano come distribuire il gateway e i dispositivi downstream in macchine virtuali di Azure. In uno scenario reale, il dispositivo e il gateway downstream vengono eseguiti su dispositivi fisici nella rete locale.

Per provare lo scenario trasparente del gateway, selezionare il pulsante seguente per distribuire due macchine virtuali Linux. Una macchina virtuale è un gateway IoT Edge trasparente, l'altro è un dispositivo downstream che simula un termostato:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Quando le due macchine virtuali vengono distribuite e in esecuzione, verificare che il IoT Edge dispositivo gateway sia in esecuzione nella `edgegateway` macchina virtuale:

1. Passare alla pagina **dispositivi** nell'applicazione IoT Central. Se il dispositivo gateway IoT Edge è connesso al IoT Central, ne viene effettuato il **provisioning**.

1. Aprire il dispositivo gateway IoT Edge e verificare lo stato dei moduli nella pagina **moduli** . Se il IoT Edge Runtime è stato avviato correttamente, lo stato dei moduli **$edgeAgent** e **$EdgeHub** è **in esecuzione**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Screenshot che mostra i moduli $edgeAgent e $edgeHub in esecuzione sul gateway di IoT Edge.":::

> [!TIP]
> Potrebbe essere necessario attendere alcuni minuti mentre viene avviata la macchina virtuale e viene effettuato il provisioning del dispositivo nell'applicazione IoT Central.

## <a name="configure-the-gateway"></a>Configurare il gateway

Per il funzionamento del dispositivo IoT Edge come gateway trasparente, sono necessari alcuni certificati per dimostrare la propria identità a tutti i dispositivi downstream. Questo articolo usa i certificati demo. In un ambiente di produzione, usare i certificati dell'autorità di certificazione.

Per generare i certificati demo e installarli nel dispositivo gateway:

1. Usare SSH per connettersi e accedere alla macchina virtuale del dispositivo gateway.

1. Eseguire i comandi seguenti per clonare il repository IoT Edge e generare i certificati demo:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Dopo aver eseguito i comandi precedenti, i file seguenti sono pronti per l'uso nei passaggi successivi:

    - *~/certs/certs/Azure-IOT-Test-only.root.ca.cert.pem* : certificato CA radice usato per creare tutti gli altri certificati dimostrativi per il test di uno scenario IOT Edge.
    - *~/certs/certs/IOT-Edge-Device-mycacert-full-chain.cert.pem* : un certificato della CA del dispositivo a cui viene fatto riferimento dal file *config. YAML* . In uno scenario di gateway, questo certificato CA rappresenta il modo in cui il dispositivo IoT Edge verifica la propria identità nei dispositivi downstream.
    - *~/certs/Private/IOT-Edge-Device-mycacert.Key.pem* : chiave privata associata al certificato della CA del dispositivo.

    Per altre informazioni su questi certificati demo, vedere [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](../../iot-edge/how-to-create-test-certificates.md).

1. Aprire il file *config. YAML* in un editor di testo. Ad esempio:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Individuare le `Certificate settings` impostazioni. Rimuovere il commento e modificare le impostazioni del certificato nel modo seguente:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    L'esempio illustrato in precedenza presuppone che sia stato eseguito l'accesso come **azureuser** e che sia stata creata una CA del dispositivo certificata denominata "mycacert".

1. Salvare le modifiche e riavviare il runtime di IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

Se il runtime di IoT Edge viene avviato correttamente dopo le modifiche, lo stato dei moduli **$edgeAgent** e **$edgeHub** viene modificato in **in esecuzione** nella pagina **moduli** del dispositivo gateway in IOT Central.

Se il runtime non si avvia, controllare le modifiche apportate in *config. YAML* e vedere [risolvere i problemi del dispositivo IOT Edge](../../iot-edge/troubleshoot.md).

Il gateway trasparente è ora configurato e pronto per l'avvio dell'invio dei dati di telemetria dai dispositivi downstream.

## <a name="provision-a-downstream-device"></a>Effettuare il provisioning di un dispositivo downstream

Attualmente, IoT Edge non è in grado di effettuare automaticamente il provisioning di un dispositivo downstream nell'applicazione IoT Central. I passaggi seguenti illustrano come eseguire il provisioning del `thermostat1` dispositivo. Per completare questi passaggi, è necessario un ambiente con Python 3,5 (o versione successiva) installato e connettività Internet. Il [Azure cloud Shell](https://shell.azure.com/) dispone di Python 3,5 pre-installato:

1. Eseguire il comando seguente per installare il `azure.iot.device` modulo:

    ```bash
    pip install azure.iot.device
    ```

1. Eseguire il comando seguente per scaricare lo script Python che esegue il provisioning:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Per effettuare il provisioning del `thermostat1` dispositivo downstream nell'applicazione IoT Central, eseguire i comandi seguenti, sostituendo `{your application id scope}` e `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

Nell'applicazione IoT Central verificare che sia stato effettuato il **provisioning** dello **stato del dispositivo** per il dispositivo thermostat1. 

## <a name="configure-a-downstream-device"></a>Configurare un dispositivo a valle

Nella sezione precedente è stata configurata la `edgegateway` macchina virtuale con i certificati demo per abilitarla per l'esecuzione come gateway. La `leafdevice` macchina virtuale è pronta per l'installazione di un simulatore del termostato che usa il gateway per connettersi a IOT Central.

`leafdevice`Per la macchina virtuale è necessaria una copia del certificato CA radice creato nella `edgegateway` macchina virtuale. Copiare il file */Home/AzureUser/certs/certs/Azure-IOT-Test-only.root.ca.cert.pem* dalla `edgegateway` macchina virtuale nella home directory della `leafdevice` macchina virtuale. È possibile usare il comando **SCP** per copiare i file da e verso una macchina virtuale Linux.

Per informazioni su come controllare la connessione dal dispositivo downstream al gateway, vedere [testare la connessione del gateway](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Per eseguire il simulatore del termostato nella `leafdevice` macchina virtuale:

1. Scaricare l'esempio Python nella home directory:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Installare il modulo Python per dispositivi Azure:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Impostare le variabili di ambiente per configurare l'esempio. Sostituire `{your device shared key}` con la chiave primaria di `thermostat1` cui si è preso nota in precedenza. Queste variabili presuppongono che il nome della macchina virtuale del gateway sia `edgegateway` e che l'ID del dispositivo termostato sia `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Si noti che la stringa di connessione usa il nome del dispositivo gateway e non il nome di un hub.

1. Per eseguire il codice, utilizzare il comando seguente:

    ```bash
    python3 simple_thermostat.py
    ```

    L'output di questo comando è simile al seguente:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Per visualizzare i dati di telemetria in IoT Central, passare alla pagina **Panoramica** per il dispositivo **thermostat1** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Screenshot che mostra i dati di telemetria dal dispositivo downstream.":::

    Nella pagina **About** è possibile visualizzare i valori delle proprietà inviati dal dispositivo downstream e nella pagina di **comando** è possibile chiamare i comandi sul dispositivo downstream.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare un gateway trasparente con IoT Central, il passaggio successivo suggerito consiste nell'acquisire ulteriori informazioni sui [IOT Edge](../../iot-edge/about-iot-edge.md).
