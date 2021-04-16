---
title: Registrare un nuovo dispositivo - Azure IoT Edge | Microsoft Docs
description: Registrare un singolo IoT Edge nell'hub IoT per il provisioning manuale con chiavi simmetriche o certificati X.509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481872"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrare un IoT Edge dispositivo nell'hub IoT

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Questo articolo illustra la procedura per registrare un nuovo IoT Edge nell'hub IoT.

Ogni dispositivo che si connette a un hub IoT ha un ID dispositivo usato per tenere traccia delle comunicazioni da cloud a dispositivo o da dispositivo a cloud. Configurare un dispositivo con le relative informazioni di connessione, che includono il nome host dell'hub IoT, l'ID del dispositivo e le informazioni che il dispositivo usa per l'autenticazione all'hub IoT.

I passaggi descritti in questo articolo illustrano un processo denominato provisioning manuale, in cui si connette un singolo dispositivo al relativo hub IoT. Per il provisioning manuale, sono disponibili due opzioni per l'autenticazione IoT Edge dispositivi:

* **Chiave simmetrica:** quando si crea una nuova identità del dispositivo nell'hub IoT, il servizio crea due chiavi. Si posiziona una delle chiavi nel dispositivo e la presenta all'hub IoT durante l'autenticazione.

  Questo metodo di autenticazione è più veloce per iniziare, ma non è così sicuro.

* **X.509 autofirmato:** si creano due certificati di identità X.509 e li si posiziona nel dispositivo. Quando si crea una nuova identità del dispositivo nell'hub IoT, si forniscono identificazioni personali da entrambi i certificati. Quando il dispositivo esegue l'autenticazione all'hub IoT, presenta un certificato e l'hub IoT verifica che il certificato corrisponda all'identificazione personale.

  Questo metodo di autenticazione è più sicuro e consigliato per gli scenari di produzione.

Questo articolo illustra entrambi i metodi di autenticazione.

Se sono disponibili molti dispositivi da configurare e non si vuole effettuare manualmente il provisioning di ognuno di essi, usare uno degli articoli seguenti per informazioni sul funzionamento di IoT Edge con il servizio Device Provisioning in hub IoT:

* [Creare ed effettuare il provisioning IoT Edge dispositivi con certificati X.509](how-to-auto-provision-x509-certs.md)
* [Creare ed effettuare il provisioning IoT Edge dispositivi con un TPM](how-to-auto-provision-simulated-device-linux.md)
* [Creare ed effettuare il provisioning IoT Edge dispositivi usando chiavi simmetriche](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Un hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) gratuito o standard nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) nell'ambiente in uso. Come minimo, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Opzione 1: Eseguire la registrazione con chiavi simmetriche

È possibile usare diversi strumenti per registrare un nuovo dispositivo IoT Edge nell'hub IoT e recuperarne la stringa di connessione, a seconda delle preferenze.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'hub IoT nel portale di Azure, IoT Edge dispositivi vengono creati e gestiti separatamente dai dispositivi IoT che non sono abilitati perimetrali.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro selezionare **IoT Edge** dal menu, quindi selezionare Aggiungi IoT Edge **dispositivo**.

   ![Aggiungere un IoT Edge dispositivo dal portale di Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Nella pagina **Crea un dispositivo** specificare le informazioni seguenti:

   * Creare un ID dispositivo descrittivo.
   * Selezionare **Chiave simmetrica** come tipo di autenticazione.
   * Usare le impostazioni predefinite per la generazione automatica delle chiavi di autenticazione e la connessione del nuovo dispositivo all'hub.

1. Selezionare **Salva**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT, è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il funzionamento di queste operazioni, è necessario accedere all'account Azure e selezionare l'hub.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. Nella parte inferiore di Explorer, espandere la sezione **Hub IoT di Azure**.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Hub IoT di Azure**. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).
1. Se ancora non lo si è fatto, eseguire l'accesso all'account di Azure seguendo le istruzioni.
1. Selezionare la sottoscrizione di Azure.
1. Selezionare l'hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrare un nuovo dispositivo con Visual Studio Code

1. In Visual Studio Code Explorer espandere la **sezione** hub IoT di Azure.
1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Hub IoT di Azure**. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).
1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* `--device-id` oppure `-d` : specificare un nome descrittivo univoco all'interno dell'hub IoT.
* `--hub-name` oppure `-n` : specificare il nome dell'hub IoT.
* `--edge-enabled` o `--ee` : dichiarare che il dispositivo è un dispositivo IoT Edge dispositivo.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Dopo aver registrato un dispositivo nell'hub IoT, recuperare la stringa di connessione che si usa per completare l'installazione e il provisioning IoT Edge runtime. Seguire la procedura descritta più avanti in questo articolo per [visualizzare i dispositivi registrati e recuperare le stringhe di connessione.](#view-registered-devices-and-retrieve-connection-strings)

## <a name="option-2-register-with-x509-certificates"></a>Opzione 2: Eseguire la registrazione con certificati X.509

Il provisioning manuale con certificati X.509 IoT Edge versione 1.0.10 o successiva.

Per l'autenticazione con certificato X.509, le informazioni  di autenticazione di ogni dispositivo vengono fornite sotto forma di identificazioni personali prese dai certificati di identità del dispositivo. Queste identificazioni vengono fornite all'hub IoT al momento della registrazione del dispositivo in modo che il servizio possa riconoscere il dispositivo quando si connette.

### <a name="create-certificates-and-thumbprints"></a>Creare certificati e identificazioni personali

Quando si effettua il provisioning IoT Edge dispositivo con certificati X.509, si usa il cosiddetto certificato *di identità del dispositivo.* Questo certificato viene usato solo per il provisioning di un IoT Edge e l'autenticazione del dispositivo con hub IoT di Azure. Si tratta di un certificato foglia che non firma altri certificati. Il certificato di identità del dispositivo è separato dai certificati dell'autorità di certificazione (CA) che il dispositivo IoT Edge presenta ai moduli o ai dispositivi downstream per la verifica. Per altre informazioni sull'uso dei certificati della CA nei dispositivi IoT Edge, vedere Informazioni su come Azure IoT Edge [i certificati.](iot-edge-certs.md)

Per il provisioning manuale con X.509 sono necessari i file seguenti:

* Due certificati di identità del dispositivo con i certificati di chiave privata corrispondenti in formato cer o pem.

  Un set di file di certificato/chiave viene fornito al runtime IoT Edge runtime. Quando si creano certificati di identità del dispositivo, impostare il nome comune del certificato con l'ID dispositivo che si vuole assegnare al dispositivo nell'hub IoT.

* Identificazioni personali provenienti da entrambi i certificati di identità del dispositivo.

  I valori di identificazione personale sono caratteri di 40 esadecimali per gli hash SHA-1 o 64 caratteri esadecimali per gli hash SHA-256. Entrambe le identificazioni vengono fornite all'hub IoT al momento della registrazione del dispositivo.

Se non sono disponibili certificati, è possibile creare certificati demo per testare le funzionalità [IoT Edge dispositivo.](how-to-create-test-certificates.md) Seguire le istruzioni in questo articolo per configurare gli script di creazione del certificato, creare un certificato CA radice e quindi creare due certificati di identità IoT Edge dispositivo.

Un modo per recuperare l'identificazione personale da un certificato è con il comando openssl seguente:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrare un nuovo dispositivo

È possibile usare diversi strumenti per registrare un nuovo IoT Edge nell'hub IoT e caricare le identificazioni personale del certificato.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'hub IoT nel portale di Azure, IoT Edge i dispositivi vengono creati e gestiti separatamente dai dispositivi IoT che non sono abilitati perimetrali.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro selezionare **IoT Edge** dal menu, quindi selezionare Aggiungi IoT Edge **dispositivo**.

   ![Aggiungere un IoT Edge dal portale di Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Nella pagina **Crea un dispositivo** specificare le informazioni seguenti:

   * Creare un ID dispositivo descrittivo. Prendere nota di questo ID dispositivo, perché verrà utilizzato nella sezione successiva.
   * Selezionare **X.509 autofirmato** come tipo di autenticazione.
   * Specificare le identificazioni personale del certificato di identità primario e secondario. I valori di identificazione personale sono 40 caratteri esadecimali per gli hash SHA-1 o 64 caratteri esadecimali per gli hash SHA-256.

1. Selezionare **Salva**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Attualmente, l'Azure IoT per Visual Studio Code non supporta la registrazione del dispositivo con certificati X.509.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Questo comando include diversi parametri:

* `--device-id` oppure `-d` : specificare un nome descrittivo univoco per l'hub IoT. Prendere nota di questo ID dispositivo, perché verrà utilizzato nella sezione successiva.
* `hub-name` o `-n` : specificare il nome dell'hub IoT.
* `--edge-enabled` o `--ee` : dichiarare che il dispositivo è un dispositivo IoT Edge dispositivo.
* `--auth-method` oppure `--am` : dichiarare il tipo di autorizzazione che il dispositivo userà. In questo caso, si usano le identificazioni personale del certificato X.509.
* `--primary-thumbprint` oppure `--ptp` : specificare un'identificazione personale del certificato X.509 da usare come chiave primaria.
* `--secondary-thumbprint` oppure `--stp` : specificare un'identificazione personale del certificato X.509 da usare come chiave secondaria.

---

Ora che si ha un dispositivo registrato nell'hub IoT, è possibile installare ed eseguire il provisioning IoT Edge runtime nel dispositivo. IoT Edge i dispositivi che eseguono l'autenticazione con certificati X.509 non usano stringhe di connessione, quindi è possibile continuare con il passaggio successivo:

* [Installare o disinstallare Azure IoT Edge per Linux](how-to-install-iot-edge.md)
* [Installare o disinstallare Azure IoT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Visualizzare i dispositivi registrati e recuperare le stringhe di connessione

I dispositivi che usano l'autenticazione con chiave simmetrica necessitano delle stringhe di connessione per completare l'installazione e il provisioning IoT Edge runtime.

I dispositivi che usano l'autenticazione del certificato X.509 non necessitano di stringhe di connessione. Questi dispositivi necessitano invece del nome dell'hub IoT, del nome del dispositivo e dei file di certificato per completare l'installazione e il provisioning IoT Edge runtime.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Usare il portale di Azure per visualizzare tutti i IoT Edge nell'hub IoT](./media/how-to-register-device/portal-view-devices.png)

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

I dispositivi che eseguono l'autenticazione con chiavi simmetriche hanno le stringhe di connessione disponibili per la copia nel portale.

1. Dalla pagina **IoT Edge** nel portale, fare clic sull'ID del dispositivo nell'elenco dei dispositivi IoT Edge.
2. Copiare il valore **Stringa di connessione primaria** o **Stringa di connessione secondaria**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visualizzare un dispositivo IoT Edge con Visual Studio Code

Tutti i dispositivi connessi all'hub IoT sono elencati nella sezione **Hub IoT di Azure** della finestra Explorer di Visual Studio Code. I dispositivi IoT Edge si distinguono dai dispositivi non Edge per la presenza di un'icona di tipo diverso e per la distribuzione dei moduli **$edgeAgent** e **$edgeHub** in ogni dispositivo IoT Edge.

![Usare VS Code per visualizzare tutti i IoT Edge nell'hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperare la stringa di connessione con Visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione **Hub IoT di Azure**.
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare i dispositivi IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) per visualizzare tutti i dispositivi nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperare la stringa di connessione con l'interfaccia della riga di comando di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare il [comando az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) per restituire la stringa di connessione per un singolo dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>Il `connection-string show` comando è stato introdotto nella versione 0.9.8 dell'Azure IoT, sostituendo il comando `show-connection-string` deprecato. Se si verifica un errore durante l'esecuzione di questo comando, assicurarsi che la versione dell'estensione sia aggiornata alla versione 0.9.8 o successiva. Per altre informazioni e gli aggiornamenti più recenti, vedere l'Microsoft Azure IoT per l'interfaccia della [riga di comando di Azure.](https://github.com/Azure/azure-iot-cli-extension)

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole.

Quando si copia la stringa di connessione da usare in un dispositivo, non includere le virgolette per la stringa di connessione.

---

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato registrato un dispositivo nell'hub IoT, è possibile installare ed eseguire il provisioning IoT Edge runtime nel dispositivo.

* [Installare o disinstallare Azure IoT Edge per Linux](how-to-install-iot-edge.md)
* [Installare o disinstallare Azure IoT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md)