---
title: Eseguire la connessione di un AZ3166 MXCHIP ad Azure IoT Central avvio rapido
description: Usare il software Azure RTO embedded per connettere un dispositivo AZ3166 MXCHIP ad Azure e inviare dati di telemetria.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: dbe5ebaef7821299dc871adfd1000a999ff6b995
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639521"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Guida introduttiva: connettere MXCHIP AZ3166 DevKit a IoT Central

**Si applica a**: [sviluppo di dispositivi incorporati](about-iot-develop.md#embedded-device-development)<br>
**Tempo di completamento totale**: 30 minuti

[![Esplora il codice](media/common/browse-code-github.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

In questa esercitazione si userà Azure RTO per connettere un MXCHIP AZ3166 DevKit (in seguito, MXCHIP DevKit) ad Azure. L'articolo fa parte della serie [Introduzione allo sviluppo di dispositivi embedded Azure](quickstart-device-development.md). La serie introduce gli sviluppatori di dispositivi ad Azure RTO e Mostra come connettere diversi kit di valutazione del dispositivo ad Azure.

Verranno completate le attività seguenti:

* Installare un set di strumenti di sviluppo incorporati per la programmazione di un DevKit di MXCHIP in C
* Compilare un'immagine e inserirla nel DevKit MXCHIP
* Usare IoT Central di Azure per creare componenti cloud, visualizzare proprietà, visualizzare i dati di telemetria del dispositivo e chiamare comandi diretti

## <a name="prerequisites"></a>Prerequisiti

* Un PC che esegue Microsoft Windows 10
* [Git](https://git-scm.com/downloads) per la clonazione del repository
* Hardware

    > * [MXCHIP AZ3166 DevKit](https://aka.ms/iot-devkit) (MXCHIP DevKit)
    > * Wi-Fi 2,4 GHz
    > * Cavo USB maschio da maschio a micro USB 2,0

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Per configurare l'ambiente di sviluppo, è prima di tutto necessario clonare un repository GitHub che contiene tutti gli asset necessari per l'esercitazione. Viene quindi installato un set di strumenti di programmazione.

### <a name="clone-the-repo-for-the-tutorial"></a>Clonare il repository per l'esercitazione

Clonare il repository seguente per scaricare tutti i codici del dispositivo di esempio, gli script di installazione e le versioni offline della documentazione. Se in precedenza il repository è stato clonato in un'altra esercitazione, non è necessario ripetere l'operazione.

Per clonare il repository, eseguire il comando seguente:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installare gli strumenti

Il repository clonato contiene uno script di installazione che consente di installare e configurare gli strumenti necessari. Se questi strumenti sono stati installati in un'altra esercitazione della Guida introduttiva, non è necessario ripetere l'operazione.

> [!NOTE]
> Tramite lo script di installazione vengono installati gli strumenti seguenti:
> * [CMake](https://cmake.org): compilazione
> * [Gcc arm](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): compilazione
> * [Termite](https://www.compuphase.com/software_termite.htm): monitorare l'output della porta seriale per i dispositivi connessi

Per installare gli strumenti:

1. Da Esplora file passare al percorso seguente nel repository ed eseguire lo script di installazione denominato *get-toolchain.bat*:

    > *getting-started\tools\get-toolchain.bat*

1. Al termine dell'installazione, aprire una nuova finestra della console per riconoscere le modifiche di configurazione apportate dallo script di installazione. Usare questa console per completare le attività di programmazione rimanenti nell'esercitazione. È possibile usare CMD di Windows, PowerShell o Git bash per Windows.
1. Eseguire il codice seguente per verificare che sia installato CMake 3,14 o versione successiva.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Creare i componenti cloud

### <a name="create-the-iot-central-application"></a>Creare l'applicazione IoT Central

Esistono diversi modi per connettere i dispositivi ad Azure. Questa sezione illustra come connettere un dispositivo usando Azure IoT Central. IoT Central è una piattaforma applicativa delle cose che riduce i costi e la complessità della creazione e della gestione delle soluzioni Internet.

Per creare una nuova applicazione:
1. Dal [portale di IOT Central di Azure](https://apps.azureiotcentral.com/)selezionare **app personali** dal menu di spostamento laterale.
1. Selezionare **+ nuova applicazione**.
1. Selezionare **App personalizzate**.
1. Aggiungere il nome dell'applicazione e un URL.
1. Scegliere il piano tariffario **gratuito** per attivare una versione di valutazione di 7 giorni.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Creare un'app personalizzata in Azure IoT Central":::

1. Selezionare **Crea**.

    Dopo che IoT Central esegue il provisioning dell'applicazione, reindirizza automaticamente al nuovo dashboard dell'applicazione.

    > [!NOTE]
    > Se si dispone di un'applicazione IoT Central esistente, è possibile utilizzarla per completare la procedura descritta in questo articolo anziché creare una nuova applicazione.

### <a name="create-a-new-device"></a>Creare un nuovo dispositivo

In questa sezione si userà il dashboard dell'applicazione IoT Central per creare un nuovo dispositivo. Si utilizzeranno le informazioni di connessione per il dispositivo appena creato per connettere in modo sicuro il dispositivo fisico in una sezione successiva.

Per creare un dispositivo:
1. Dal dashboard dell'applicazione selezionare **dispositivi** dal menu di spostamento laterale.
1. Selezionare **+ nuovo** per aprire la finestra **Crea un nuovo dispositivo** .
1. Lasciare il modello di dispositivo non **assegnato**.
1. Immettere il nome del dispositivo e l'ID del dispositivo desiderati.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Creare un dispositivo in Azure IoT Central":::

1. Selezionare il pulsante **Crea**.
1. Il dispositivo appena creato verrà visualizzato nell'elenco **tutti i dispositivi** .  Selezionare il nome del dispositivo per visualizzare i dettagli.
1. Selezionare **Connetti** nella barra dei menu in alto a destra per visualizzare le informazioni di connessione usate per configurare il dispositivo nella sezione successiva.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Visualizza i dettagli della connessione del dispositivo":::

1. Prendere nota dei valori di connessione per i seguenti parametri della stringa di connessione visualizzati nella finestra di dialogo **Connetti** . Questi valori verranno aggiunti a un file di configurazione nel passaggio successivo:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Preparare il dispositivo

Per connettere il DevKit di MXCHIP ad Azure, si modificherà un file di configurazione per le impostazioni Wi-Fi e Azure Internet, si ricreerà l'immagine e si lampeggerà l'immagine sul dispositivo.

### <a name="add-configuration"></a>Aggiungere la configurazione

1. Aprire il file seguente in un editor di testo:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Impostare le costanti Wi-Fi sui valori seguenti dall'ambiente locale.

    |Nome costante|Valore|
    |-------------|-----|
    |`WIFI_SSID` |{*Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*La password Wi-Fi*}|
    |`WIFI_MODE` |{*Uno dei valori enumerati della modalità di Wi-Fi nel file*}|

1. Impostare le costanti informazioni sul dispositivo Azure per i valori salvati dopo aver creato le risorse di Azure.

    |Nome costante|Valore|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Valore dell'ambito ID*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Valore ID dispositivo*}|
    |`IOT_DEVICE_SAS_KEY` |{*Valore della chiave primaria*}|

1. Salvare e chiudere il file.

### <a name="build-the-image"></a>Compilare l'immagine

Nella console o in Esplora file, eseguire lo script *rebuild.bat* nel percorso seguente per compilare l'immagine:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Al termine della compilazione, verificare che il file binario sia stato creato nel percorso seguente:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>Flash immagine

1. Nel DevKit MXCHIP individuare il pulsante **Reimposta** e la porta micro USB. Questi componenti vengono usati nei passaggi seguenti. Entrambi sono evidenziati nell'immagine seguente:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Individuare i componenti chiave nella bacheca MXChip DevKit":::

1. Connettere il cavo micro USB alla porta micro USB sulla DevKit di MXCHIP e quindi connetterlo al computer.
1. In Esplora file trovare il file binario creato nella sezione precedente.
1. Copiare il file binario *mxchip_azure_iot. bin*.
1. In Esplora file trovare il dispositivo MXCHIP DevKit connesso al computer. Il dispositivo viene visualizzato come unità nel sistema con l'etichetta dell'unità **AZ3166**.
1. Incollare il file binario nella cartella radice di MXCHIP devkit. Il lampeggio viene avviato automaticamente e viene completato in pochi secondi.

    > [!NOTE]
    > Durante il processo lampeggiante, un LED verde si attiva in MXCHIP DevKit.

### <a name="confirm-device-connection-details"></a>Confermare i dettagli della connessione del dispositivo

È possibile usare l'app **termite** per monitorare la comunicazione e verificare che il dispositivo sia configurato correttamente.

1. Avvia la **termite**.
    > [!TIP]
    > Se non è possibile connettere la termite alla DevKit, installare il [driver St-link](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) e riprovare. Per ulteriori passaggi, vedere  [risoluzione dei problemi](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) .
1. Selezionare **Impostazioni**.
1. Nella finestra di dialogo **Impostazioni porta seriale** controllare le impostazioni seguenti e aggiornare se necessario:
    * **Velocità in baud**: 115.200
    * **Port**: la porta a cui è connessa la DevKit MXCHIP. Se nell'elenco a discesa sono presenti più opzioni di porta, è possibile trovare la porta corretta da usare. Aprire Windows **Gestione dispositivi** e visualizzare le **porte** per identificare la porta da utilizzare.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Confermare le impostazioni nell'app termite":::

1. Selezionare OK.
1. Premere il pulsante **Reimposta** sul dispositivo. Il pulsante è contrassegnato sul dispositivo e posizionato vicino al connettore micro USB.
1. Nell'app **termite** verificare i valori dei checkpoint seguenti per verificare che il dispositivo sia stato inizializzato e connesso ad Azure.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Mantieni la termite aperta per monitorare l'output del dispositivo nei passaggi seguenti.

## <a name="verify-the-device-status"></a>Verificare lo stato del dispositivo

Per visualizzare lo stato del dispositivo nel portale di IoT Central:
1. Dal dashboard dell'applicazione selezionare **dispositivi** dal menu di spostamento laterale.
1. Verificare che lo **stato del dispositivo** sia aggiornato in **provisioning** eseguito.
1. Verificare che il **modello di dispositivo** sia aggiornato alla **guida introduzione**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Visualizza lo stato del dispositivo in IoT Central":::

## <a name="view-telemetry"></a>Visualizzare i dati di telemetria

Con IoT Central, è possibile visualizzare il flusso di dati di telemetria dal dispositivo al cloud.

Per visualizzare i dati di telemetria nel portale IoT Central:

1. Dal dashboard dell'applicazione selezionare **dispositivi** dal menu di spostamento laterale.
1. Selezionare il dispositivo dall'elenco dei dispositivi.
1. Visualizzare i dati di telemetria quando il dispositivo invia messaggi al cloud nella scheda **Panoramica** .

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Visualizzare i dati di telemetria del dispositivo in IoT Central":::

    > [!NOTE]
    > È anche possibile monitorare i dati di telemetria dal dispositivo usando l'app termite.

## <a name="call-a-direct-method-on-the-device"></a>Chiamare un metodo diretto nel dispositivo

È anche possibile usare IoT Central per chiamare un metodo diretto implementato nel dispositivo. I metodi diretti hanno un nome e possono facoltativamente avere un payload JSON, una connessione configurabile e un timeout del metodo. In questa sezione viene chiamato un metodo che consente di attivare o disattivare un LED.

Per chiamare un metodo nel portale di IoT Central:

1. Selezionare la scheda **comando** dalla pagina dispositivo.
1. Selezionare **stato** e selezionare **Esegui**.  La luce del LED dovrebbe accendersi.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Chiamare un metodo diretto in un dispositivo":::

1. Deselezionare **stato** e selezionare **Esegui**. La luce del LED dovrebbe spegnersi.

## <a name="view-device-information"></a>Visualizza informazioni sul dispositivo

È possibile visualizzare le informazioni sul dispositivo da IoT Central.

Selezionare scheda **informazioni** nella pagina dispositivo.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Visualizzare informazioni sul dispositivo in IoT Central":::

## <a name="debugging"></a>Debug

Per eseguire il debug dell'applicazione, vedere [debug con Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse di Azure create in questa esercitazione non sono più necessarie, è possibile eliminarle dal portale di IoT Central. Facoltativamente, se si continua con un'altra esercitazione in questa guida Introduzione, è possibile mantenere le risorse già create e riutilizzarle.

Per rimuovere l'intera applicazione di esempio IoT Central di Azure e tutti i relativi dispositivi e risorse:
1. Selezionare **Amministrazione**  >  **dell'applicazione**.
1. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata compilata un'immagine personalizzata che contiene il codice di esempio di Azure RTO, quindi l'immagine è stata intermittente con il dispositivo MXCHIP DevKit. È stato anche usato il portale di IoT Central per creare risorse di Azure, connettere il DevKit di MXCHIP in modo sicuro ad Azure, visualizzare i dati di telemetria e inviare messaggi.

* Per gli sviluppatori di dispositivi, il passaggio successivo suggerito consiste nel vedere le altre esercitazioni della serie [Introduzione allo sviluppo di dispositivi incorporati in Azure](quickstart-device-development.md).
* Se si verificano problemi durante l'inizializzazione o la connessione del dispositivo dopo aver seguito i passaggi descritti in questa guida, vedere [risoluzione dei](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)problemi.
* Per altre informazioni sul modo in cui i componenti di Azure RTO vengono usati nel codice di esempio per questa esercitazione, vedere [uso di Azure RTO nella guida introduzione](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > Azure RTO offre agli OEM componenti per proteggere le comunicazioni e creare codice e isolamento dei dati usando meccanismi di protezione hardware MCU/MPU sottostanti. Tuttavia, ogni OEM è responsabile di garantire che il dispositivo soddisfi i requisiti di sicurezza in evoluzione.

