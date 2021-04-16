---
title: Autenticare i dispositivi downstream - Azure IoT Edge | Microsoft Docs
description: Come autenticare i dispositivi downstream o i dispositivi foglia nell'hub IoT e instradare la relativa connessione tramite i dispositivi gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4702682dcd6af68242fd5a34d1fb2e0a9273da36
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482025"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticare un dispositivo downstream con l'hub IoT di Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

In uno scenario di gateway trasparente, i dispositivi downstream (talvolta detti dispositivi foglia o dispositivi figlio) necessitano di identità nell'hub IoT come qualsiasi altro dispositivo. Questo articolo illustra le opzioni per l'autenticazione di un dispositivo downstream nell'hub IoT e spiega quindi come dichiarare la connessione gateway.

Ci sono tre passaggi generali per configurare correttamente una connessione gateway trasparente. Questo articolo illustra il secondo passaggio:

1. Configurare il dispositivo gateway come server in modo che i dispositivi downstream possano connettersi al dispositivo in modo sicuro. Configurare il gateway per ricevere messaggi dai dispositivi downstream e instradare i messaggi alla destinazione appropriata. Per questi passaggi, vedere [Configurare un dispositivo IoT Edge come gateway trasparente.](how-to-create-transparent-gateway.md)
2. **Creare un'identità del dispositivo per il dispositivo downstream in modo che possa eseguire l'autenticazione con l'hub IoT. Configurare il dispositivo downstream per inviare messaggi tramite il dispositivo gateway.**
3. Connettere il dispositivo downstream al dispositivo gateway e iniziare a inviare messaggi. Per questi passaggi, vedere [Connettere un dispositivo downstream a un Azure IoT Edge gateway](how-to-connect-downstream-device.md).

I dispositivi downstream possono eseguire l'autenticazione nell'hub IoT usando uno dei tre metodi seguenti: chiavi simmetriche (talvolta dette chiavi di accesso condiviso), certificati X.509 autofirmati o certificati X.509 firmati dall'autorità di certificazione (CA). I passaggi di autenticazione sono simili a quelli usati per configurare qualsiasi dispositivo non IoT Edge con l'hub IoT, con piccole differenze per dichiarare la relazione del gateway.

Il provisioning automatico dei dispositivi downstream con hub IoT di Azure Device Provisioning Service (DPS) non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi in [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).

Se si usa l'autenticazione X.509, verranno generati certificati per il dispositivo downstream. Avere lo stesso certificato DELLA CA radice e lo script di generazione del certificato usato per l'articolo sul gateway trasparente per usarlo di nuovo.

Questo articolo si riferisce al *nome host del gateway*  in diversi punti. Il nome host del gateway viene dichiarato nel **parametro hostname** del file di configurazione nel dispositivo IoT Edge gateway. Viene fatto riferimento a tale valore nella stringa di connessione del dispositivo downstream. Il nome host del gateway deve essere risolvibile in un indirizzo IP, usando DNS o una voce del file host nel dispositivo downstream.

## <a name="register-device-with-iot-hub"></a>Registrare il dispositivo con l'hub IoT

Scegliere la modalità di autenticazione del dispositivo downstream con l'hub IoT:

* [Autenticazione con chiave simmetrica:](#symmetric-key-authentication)l'hub IoT crea una chiave inserita nel dispositivo downstream. Quando il dispositivo esegue l'autenticazione, l'hub IoT verifica che le due chiavi corrispondano. Non è necessario creare certificati aggiuntivi per usare l'autenticazione con chiave simmetrica.

  Questo metodo è più rapido per iniziare se si testano i gateway in uno scenario di sviluppo o test.

* [Autenticazione autofirmata X.509:](#x509-self-signed-authentication)talvolta denominata autenticazione dell'identificazione personale, perché si condivide l'identificazione personale dal certificato X.509 del dispositivo con l'hub IoT.

  L'autenticazione del certificato è consigliata per i dispositivi negli scenari di produzione.

* [Autenticazione con firma CA X.509:](#x509-ca-signed-authentication)caricare il certificato ca radice nell'hub IoT. Quando i dispositivi presentano il certificato X.509 per l'autenticazione, l'hub IoT verifica che appartenga a una catena di certificati firmata dallo stesso certificato CA radice.

  L'autenticazione del certificato è consigliata per i dispositivi in scenari di produzione.

### <a name="symmetric-key-authentication"></a>Autenticazione con chiavi simmetriche

L'autenticazione tramite chiave simmetrica, o autenticazione tramite chiave di accesso condiviso, è il modo più semplice per eseguire l'autenticazione nell'hub IoT. Con l'autenticazione tramite chiave simmetrica, una chiave Base64 viene associata all'ID dispositivo IoT nell'hub IoT. Questa chiave viene inclusa nelle applicazioni IoT, in modo che il dispositivo possa presentarla quando si connette all'hub IoT.

Aggiungere un nuovo dispositivo IoT nell'hub IoT usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'estensione IoT per Visual Studio Code. Tenere presente che i dispositivi downstream devono essere identificati nell'hub IoT come normali dispositivi IoT, non come dispositivi IoT Edge.

Quando si crea la nuova identità del dispositivo, fornire le informazioni seguenti:

* Creare un ID per il dispositivo.

* Selezionare **Chiave simmetrica** come tipo di autenticazione.

* Selezionare **Imposta un dispositivo padre** e selezionare il dispositivo gateway IoT Edge cui si connetterà il dispositivo downstream. È sempre possibile modificare l'elemento padre in un secondo momento.

   ![Creare l'ID dispositivo con l'autenticazione tramite chiave simmetrica nel portale](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >L'impostazione del dispositivo padre era un passaggio facoltativo per i dispositivi downstream che usano l'autenticazione con chiave simmetrica. Tuttavia, a partire IoT Edge versione 1.1.0 ogni dispositivo downstream deve essere assegnato a un dispositivo padre.
   >
   >È possibile configurare l'hub IoT Edge per tornare al comportamento precedente impostando la variabile di ambiente **AuthenticationMode** sul **valore CloudAndScope**.

È anche possibile usare l'estensione [IoT per l'interfaccia della riga di comando](https://github.com/Azure/azure-iot-cli-extension) di Azure per completare la stessa operazione. L'esempio seguente usa [il comando az iot hub device-identity](/cli/azure/iot/hub/device-identity) per creare un nuovo dispositivo IoT con autenticazione con chiave simmetrica e assegnare un dispositivo padre:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo disponga delle informazioni per la connessione tramite il gateway.

### <a name="x509-self-signed-authentication"></a>Autenticazione autofirmata X.509

Per l'autenticazione autofirmata X.509, talvolta definita autenticazione con identificazione personale, è necessario creare certificati da inserire nel dispositivo downstream. Questi certificati hanno un'identificazione personale che viene condivisa con l'hub IoT per l'autenticazione.

1. Usando il certificato della CA, creare due certificati del dispositivo (primario e secondario) per il dispositivo downstream.

   Se non si dispone di un'autorità di certificazione per creare certificati X.509, è possibile usare gli script dei certificati demo IoT Edge creare certificati [dispositivo downstream.](how-to-create-test-certificates.md#create-downstream-device-certificates) Seguire i passaggi per la creazione di certificati autofirmati. Usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

   Se si creano certificati personalizzati, assicurarsi che il nome del soggetto del certificato del dispositivo sia impostato sull'ID dispositivo da usare durante la registrazione del dispositivo IoT nel hub IoT di Azure. Questa impostazione è obbligatoria per l'autenticazione.

2. Recuperare l'impronta digitale SHA1 (detta identificazione personale nell'interfaccia dell'hub IoT) di ogni certificato, costituita da una stringa esadecimale di 40 caratteri. Usare il comando OpenSSL seguente per visualizzare il certificato e trovare l'impronta digitale:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Eseguire questo comando due volte, una volta per il certificato primario e una per il certificato secondario. Si forniscono le impronte digitali per entrambi i certificati quando si registra un nuovo dispositivo IoT usando certificati X.509 autofirmati.

3. Passare all'hub IoT nel portale di Azure e creare una nuova identità del dispositivo IoT con i valori seguenti:

   * Fornire l'**ID dispositivo** corrispondente al nome soggetto dei certificati del dispositivo.
   * Selezionare **X.509 autofirmato** come tipo di autenticazione.
   * Incollare le stringhe esadecimali copiate dai certificati primario e secondario del dispositivo.
   * Selezionare **Imposta un dispositivo padre** e scegliere il dispositivo gateway IoT Edge tramite cui si connetterà il dispositivo downstream. È sempre possibile modificare l'elemento padre in un secondo momento.

   ![Creare l'ID dispositivo con l'opzione di autenticazione X.509 autofirmato nel portale](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copiare i certificati del dispositivo primario e secondario e le relative chiavi in qualsiasi posizione nel dispositivo downstream. Spostare inoltre una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati dei dispositivi downstream.

   Si fa riferimento a questi file di certificato in tutte le applicazioni nel dispositivo downstream che si connettono all'hub IoT. È possibile usare un servizio come [Azure Key Vault](../key-vault/index.yml) o una funzione come il [protocollo Secure Copy](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.

5. A seconda del linguaggio preferito, vedere gli esempi di come fare riferimento ai certificati X.509 nelle applicazioni IoT:

   * C#: [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È anche possibile usare [l'estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) per completare la stessa operazione di creazione del dispositivo. L'esempio seguente usa il [comando az iot hub device-identity](/cli/azure/iot/hub/device-identity) per creare un nuovo dispositivo IoT con autenticazione autofirmata X.509 e assegna un dispositivo padre:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo disponga delle informazioni per la connessione tramite il gateway.

### <a name="x509-ca-signed-authentication"></a>Autenticazione con firma CA X.509

Per l'autenticazione con firma dell'autorità di certificazione X.509, è necessario un certificato ca radice registrato nell'hub IoT da usare per firmare i certificati per il dispositivo downstream. Qualsiasi dispositivo che usa un certificato che è stato rilasciato tramite il certificato CA radice o qualsiasi certificato intermedio sarà autorizzato a eseguire l'autenticazione.

Questa sezione si basa sulle istruzioni illustrate nell'articolo relativo all'hub IoT [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md).

1. Usando il certificato della CA, creare due certificati del dispositivo (primario e secondario) per il dispositivo downstream.

   Se non si dispone di un'autorità di certificazione per creare certificati X.509, è possibile usare gli script del certificato IoT Edge demo per creare certificati dispositivo [downstream.](how-to-create-test-certificates.md#create-downstream-device-certificates) Seguire la procedura per la creazione di certificati firmati da ca. Usare lo stesso certificato ca radice che ha generato i certificati per il dispositivo gateway.

2. Seguire le istruzioni riportate nella sezione [Registrare i certificati della CA X.509 nell'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) dell'articolo *Configurare la sicurezza X.509 nell'hub IoT di Azure*. In tale sezione si eseguono i passaggi seguenti:

   1. Aggiornare un certificato CA radice. Se si usano i certificati demo, la CA radice è **\<path> /certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Verificare di possedere il certificato CA radice.

3. Seguire le istruzioni riportate nella sezione [Creare un dispositivo X.509 per l'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) dell'articolo *Configurare la sicurezza X.509 nell'hub IoT di Azure*. In tale sezione si eseguono i passaggi seguenti:

   1. Aggiungere un nuovo dispositivo. Specificare un nome in lettere minuscole per **ID dispositivo** e scegliere il tipo di autenticazione **X.509 firmato dall'Autorità di certificazione**.

   2. Impostare un dispositivo padre. Selezionare **Imposta un dispositivo padre e** scegliere il dispositivo IoT Edge gateway che fornirà la connessione all'hub IoT.

4. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso certificato CA radice caricato nell'hub IoT per creare la catena. Usare lo stesso ID dispositivo in lettere minuscole assegnato all'identità del dispositivo nel portale.

5. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare inoltre una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati dei dispositivi downstream.

   Si fa riferimento a questi file in tutte le applicazioni nel dispositivo downstream che si connettono all'hub IoT. È possibile usare un servizio come [Azure Key Vault](../key-vault/index.yml) o una funzione come il [protocollo Secure Copy](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.

6. A seconda del linguaggio preferito, vedere gli esempi di come fare riferimento ai certificati X.509 nelle applicazioni IoT:

   * C#: [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È anche possibile usare [l'estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) per completare la stessa operazione di creazione del dispositivo. L'esempio seguente usa il [comando az iot hub device-identity](/cli/azure/iot/hub/device-identity) per creare un nuovo dispositivo IoT con autenticazione firmata dalla CA X.509 e assegna un dispositivo padre:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo disponga delle informazioni per la connessione tramite il gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperare e modificare la stringa di connessione

Dopo aver creato un'identità del dispositivo IoT nel portale, è possibile recuperare le relative chiavi primaria e secondaria. Una di queste chiavi deve essere inclusa nella stringa di connessione usata dalle applicazioni per comunicare con l'hub IoT. Per l'autenticazione tramite chiave simmetrica, l'hub IoT fornisce la stringa di connessione completa nei dati del dispositivo, per praticità. È necessario aggiungere ulteriori informazioni sul dispositivo gateway nella stringa di connessione.

Per le stringhe di connessione per i dispositivi downstream sono necessari i componenti seguenti:

* Hub IoT a cui il dispositivo si connette: `Hostname={iothub name}.azure-devices.net`
* ID dispositivo registrato nell'hub: `DeviceID={device ID}`
* Metodo di autenticazione, sia con chiave simmetrica che con certificati X.509
  * Se si usa l'autenticazione con chiave simmetrica, specificare la chiave primaria o secondaria: `SharedAccessKey={key}`
  * Se si usa l'autenticazione del certificato X.509, specificare un flag: `x509=true`
* Dispositivo gateway attraverso cui il dispositivo si connette. Specificare il **valore del nome** host dal IoT Edge di configurazione del dispositivo gateway: `GatewayHostName={gateway hostname}`

Nel complesso, una stringa di connessione completa ha un aspetto simile al seguente:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Oppure:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Grazie alla relazione padre/figlio, è possibile semplificare la stringa di connessione chiamando il gateway direttamente come host di connessione. Ad esempio:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Questa stringa di connessione modificata verrà utilizzata nell'articolo successivo della serie di gateway trasparenti.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, è presente un dispositivo IoT Edge registrato con l'hub IoT e configurato come gateway trasparente. È anche disponibile un dispositivo downstream registrato con l'hub IoT e che punta al dispositivo gateway.

Successivamente, è necessario configurare il dispositivo downstream per considerare attendibile il dispositivo gateway e connettersi al dispositivo in modo sicuro. Continuare con l'articolo successivo della serie sul gateway trasparente, [Connettere un dispositivo downstream a un gateway Azure IoT Edge trasparente.](how-to-connect-downstream-device.md)