---
title: Registrare un nuovo dispositivo-Azure IoT Edge | Microsoft Docs
description: Registrare un singolo dispositivo IoT Edge nell'hub Internet per il provisioning manuale con chiavi simmetriche o certificati X. 509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 97faf5eff7187bbabe23bbcab60514eef4acc063
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634193"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrare un dispositivo IoT Edge nell'hub Internet

Questo articolo illustra i passaggi per registrare un nuovo dispositivo IoT Edge nell'hub Internet.

Ogni dispositivo che si connette a un hub Internet ha un ID dispositivo usato per tenere traccia delle comunicazioni da cloud a dispositivo o da dispositivo a cloud. Un dispositivo viene configurato con le relative informazioni di connessione, che include il nome host dell'hub Internet, l'ID del dispositivo e le informazioni usate dal dispositivo per l'autenticazione nell'hub.

La procedura illustrata in questo articolo descrive un processo denominato provisioning manuale, in cui si connette un singolo dispositivo all'hub Internet. Per il provisioning manuale, sono disponibili due opzioni per l'autenticazione dei dispositivi IoT Edge:

* **Chiave simmetrica**: quando si crea una nuova identità del dispositivo nell'hub Internet, il servizio crea due chiavi. Si inserisce una delle chiavi nel dispositivo e viene visualizzata la chiave nell'hub Internet durante l'autenticazione.

  Questo metodo di autenticazione è più veloce per iniziare, ma non come protetto.

* **X. 509 autofirmato**: si creano due certificati di identità x. 509 e li si inserisce nel dispositivo. Quando si crea una nuova identità del dispositivo nell'hub Internet, è necessario fornire le identificazioni personali di entrambi i certificati. Quando il dispositivo esegue l'autenticazione nell'hub Internet, presenta un certificato e l'hub Internet verifica che il certificato corrisponda all'identificazione personale.

  Questo metodo di autenticazione è più sicuro e consigliato per gli scenari di produzione.

Questo articolo illustra entrambi i metodi di autenticazione.

Se sono presenti molti dispositivi da configurare e non si vuole eseguire manualmente il provisioning di ognuno di essi, usare uno degli articoli seguenti per informazioni su come IoT Edge funziona con il servizio Device provisioning in hub Internet:

* [Creare ed effettuare il provisioning di dispositivi IoT Edge usando certificati X. 509](how-to-auto-provision-x509-certs.md)
* [Creare ed effettuare il provisioning di dispositivi IoT Edge con un TPM](how-to-auto-provision-simulated-device-linux.md)
* [Creare ed effettuare il provisioning di dispositivi IoT Edge usando chiavi simmetriche](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Un [Hub](../iot-hub/iot-hub-create-through-portal.md) delle cose gratuito o standard nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) gratuito o standard nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) nell'ambiente in uso. Come minimo, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Opzione 1: registrare con chiavi simmetriche

È possibile usare diversi strumenti per registrare un nuovo dispositivo IoT Edge nell'hub Internet e recuperare la relativa stringa di connessione, a seconda delle proprie preferenze.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'hub Internet delle cose nel portale di Azure IoT Edge i dispositivi vengono creati e gestiti separatamente dai dispositivi Internet che non sono abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro selezionare **IOT Edge** dal menu e quindi selezionare **aggiungi un dispositivo IOT Edge**.

   ![Aggiungere un dispositivo IoT Edge dalla portale di Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Nella pagina **Crea un dispositivo** specificare le informazioni seguenti:

   * Creare un ID dispositivo descrittivo.
   * Selezionare **Chiave simmetrica** come tipo di autenticazione.
   * Usare le impostazioni predefinite per la generazione automatica delle chiavi di autenticazione e la connessione del nuovo dispositivo all'hub.

1. Selezionare **Salva**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT, è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il corretto funzionamento di queste operazioni, è necessario accedere al proprio account Azure e selezionare l'hub.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. Nella parte inferiore di Explorer, espandere la sezione **Hub IoT di Azure**.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Hub IoT di Azure**. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).
1. Se ancora non lo si è fatto, eseguire l'accesso all'account di Azure seguendo le istruzioni.
1. Selezionare la sottoscrizione di Azure.
1. Selezionare l'hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrare un nuovo dispositivo con Visual Studio Code

1. In Esplora Visual Studio Code espandere la sezione **Hub Azure** .
1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Hub IoT di Azure**. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).
1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* `--device-id``-d`in alternativa, specificare un nome descrittivo univoco all'interno dell'hub Internet.
* `--hub-name` in alternativa `-n` , specificare il nome dell'hub Internet delle cose.
* `--edge-enabled` oppure `--ee` : dichiarare che il dispositivo è un dispositivo IOT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Ora che si dispone di un dispositivo registrato nell'hub Internet, recuperare la stringa di connessione usata per completare l'installazione e il provisioning del IoT Edge Runtime. Seguire i passaggi più avanti in questo articolo per [visualizzare i dispositivi registrati e recuperare le stringhe di connessione](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Opzione 2: registrare con certificati X. 509

Il provisioning manuale con certificati X. 509 richiede IoT Edge versione 1.0.10 o successiva.

Per l'autenticazione del certificato X. 509, le informazioni di autenticazione di ogni dispositivo vengono fornite sotto forma di *identificazione personale* ottenuta dai certificati di identità del dispositivo. Queste identificazioni personali vengono assegnate all'hub delle cose al momento della registrazione del dispositivo, in modo che il servizio possa riconoscere il dispositivo quando si connette.

### <a name="create-certificates-and-thumbprints"></a>Creare certificati e identificazioni personali

Quando si esegue il provisioning di un dispositivo IoT Edge con certificati X. 509, viene usato un *certificato di identità del dispositivo*. Questo certificato viene usato solo per il provisioning di un dispositivo IoT Edge e l'autenticazione del dispositivo con l'hub Azure. Si tratta di un certificato foglia che non firma altri certificati. Il certificato di identità del dispositivo è separato dai certificati dell'autorità di certificazione (CA) che il dispositivo di IoT Edge presenta ai moduli o ai dispositivi downstream per la verifica. Per ulteriori informazioni sulla modalità di utilizzo dei certificati della CA nei dispositivi IoT Edge, vedere informazioni su [come Azure IOT Edge utilizza i certificati](iot-edge-certs.md).

Sono necessari i file seguenti per il provisioning manuale con X. 509:

* Due dei certificati di identità del dispositivo con i certificati di chiave privata corrispondenti nei formati con estensione cer o PEM.

  Un set di file di certificato/chiave viene fornito al runtime IoT Edge. Quando si creano i certificati di identità del dispositivo, impostare il nome comune del certificato (CN) con l'ID del dispositivo che si vuole che il dispositivo abbia nell'hub.

* Identificazioni personali ottenute da entrambi i certificati di identità del dispositivo.

  I valori di identificazione personale sono 40 caratteri esadecimali per gli hash SHA-1 o i caratteri esadecimali 64 per gli hash SHA-256. Entrambe le identificazioni personali vengono fornite all'hub delle cose al momento della registrazione del dispositivo.

Se non sono disponibili certificati, è possibile [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md). Seguire le istruzioni riportate in questo articolo per configurare gli script di creazione del certificato, creare un certificato CA radice e quindi creare due IoT Edge certificati di identità del dispositivo.

Un modo per recuperare l'identificazione personale da un certificato è con il comando OpenSSL seguente:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrare un nuovo dispositivo

È possibile usare diversi strumenti per registrare un nuovo dispositivo IoT Edge nell'hub Internet e caricare le relative identificazioni personali del certificato.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'hub Internet delle cose nel portale di Azure IoT Edge i dispositivi vengono creati e gestiti separatamente dai dispositivi Internet che non sono abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro selezionare **IOT Edge** dal menu e quindi selezionare **aggiungi un dispositivo IOT Edge**.

   ![Aggiungere un dispositivo IoT Edge dalla portale di Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Nella pagina **Crea un dispositivo** specificare le informazioni seguenti:

   * Creare un ID dispositivo descrittivo. Prendere nota di questo ID dispositivo, che verrà usato nella sezione successiva.
   * Selezionare **X.509 autofirmato** come tipo di autenticazione.
   * Fornire le identificazioni personali del certificato di identità primaria e secondaria. I valori di identificazione personale sono 40 caratteri esadecimali per gli hash SHA-1 o i caratteri esadecimali 64 per gli hash SHA-256.

1. Selezionare **Salva**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Attualmente l'estensione Azure per la Visual Studio Code non supporta la registrazione del dispositivo con certificati X. 509.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Questo comando include diversi parametri:

* `--device-id``-d`in alternativa, specificare un nome descrittivo univoco per l'hub Internet. Prendere nota di questo ID dispositivo, che verrà usato nella sezione successiva.
* `hub-name` in alternativa `-n` , specificare il nome dell'hub Internet delle cose.
* `--edge-enabled` oppure `--ee` : dichiarare che il dispositivo è un dispositivo IOT Edge.
* `--auth-method``--am`in alternativa, dichiarare il tipo di autorizzazione che verrà usato dal dispositivo. In questo caso, vengono usate le identificazioni personali del certificato X. 509.
* `--primary-thumbprint` o `--ptp` : fornire un'identificazione personale del certificato X. 509 da utilizzare come chiave primaria.
* `--secondary-thumbprint` o `--stp` : fornire un'identificazione personale del certificato X. 509 da usare come chiave secondaria.

---

Ora che si dispone di un dispositivo registrato nell'hub Internet, si è pronti per installare ed eseguire il provisioning del IoT Edge Runtime nel dispositivo. IoT Edge i dispositivi che eseguono l'autenticazione con i certificati X. 509 non utilizzano stringhe di connessione, è quindi possibile procedere con il passaggio successivo:

* [Installare o disinstallare Azure IoT Edge per Linux](how-to-install-iot-edge.md)
* [Installare o disinstallare Azure IoT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Visualizzare i dispositivi registrati e recuperare le stringhe di connessione

Per i dispositivi che usano l'autenticazione con chiave simmetrica sono necessarie le stringhe di connessione per completare l'installazione e il provisioning del IoT Edge Runtime.

I dispositivi che usano l'autenticazione del certificato X. 509 non necessitano di stringhe di connessione. Per completare l'installazione e il provisioning del runtime di IoT Edge, questi dispositivi devono invece avere il nome dell'hub Internet, il nome del dispositivo e i relativi file di certificato.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Usare il portale di Azure per visualizzare tutti i dispositivi IoT Edge nell'hub Internet delle cose](./media/how-to-register-device/portal-view-devices.png)

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

I dispositivi che eseguono l'autenticazione con chiavi simmetriche hanno le stringhe di connessione disponibili per la copia nel portale.

1. Dalla pagina **IoT Edge** nel portale, fare clic sull'ID del dispositivo nell'elenco dei dispositivi IoT Edge.
2. Copiare il valore **Stringa di connessione primaria** o **Stringa di connessione secondaria**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visualizzare un dispositivo IoT Edge con Visual Studio Code

Tutti i dispositivi connessi all'hub IoT sono elencati nella sezione **Hub IoT di Azure** della finestra Explorer di Visual Studio Code. I dispositivi IoT Edge si distinguono dai dispositivi non Edge per la presenza di un'icona di tipo diverso e per la distribuzione dei moduli **$edgeAgent** e **$edgeHub** in ogni dispositivo IoT Edge.

![Usare VS Code per visualizzare tutti i dispositivi IoT Edge nell'hub Internet delle cose](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperare la stringa di connessione con Visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione **Hub IoT di Azure**.
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare i dispositivi IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) per visualizzare tutti i dispositivi nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperare la stringa di connessione con l'interfaccia della riga di comando di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare il comando [AZ all Hub Device-Identity Connection-String Show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) per restituire la stringa di connessione per un singolo dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>Il `connection-string show` comando è stato introdotto nella versione 0.9.8 dell'estensione Azure Internet, sostituendo il comando deprecato `show-connection-string` . Se si verifica un errore durante l'esecuzione di questo comando, assicurarsi che la versione dell'estensione venga aggiornata a 0.9.8 o versioni successive. Per altre informazioni e per gli aggiornamenti più recenti, vedere [Microsoft Azure estensione Internet per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole.

Quando si copia la stringa di connessione da usare in un dispositivo, non includere le virgolette intorno alla stringa di connessione.

---

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un dispositivo registrato nell'hub Internet, si è pronti per installare ed eseguire il provisioning del IoT Edge Runtime nel dispositivo.

* [Installare o disinstallare Azure IoT Edge per Linux](how-to-install-iot-edge.md)
* [Installare o disinstallare Azure IoT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md)