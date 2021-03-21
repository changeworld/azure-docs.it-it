---
title: Eseguire la distribuzione in un IoT Edge per Linux in Windows-Azure
description: Questo articolo fornisce indicazioni su come eseguire la distribuzione in un IoT Edge per Linux in un dispositivo Windows.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 9ec28c62ca804137ede3cd60d1980e55fbaa2807
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618135"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Eseguire la distribuzione in un dispositivo IoT Edge per Linux in Windows (EFLOW)

In questo articolo si apprenderà come distribuire analisi video in tempo reale in un dispositivo perimetrale con [IOT Edge per Linux in Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows). Dopo aver completato i passaggi descritti in questo documento, sarà possibile eseguire un [grafico multimediale](media-graph-concept.md) che rilevi il movimento in un video ed emette tali eventi nell'hub Internet nel cloud. Puoi quindi disattivare il grafico multimediale per gli scenari avanzati e sfruttare la potenza di analisi video in tempo reale sul dispositivo IoT Edge basato su Windows.

## <a name="prerequisites"></a>Prerequisiti 

* Un account Azure con una sottoscrizione attiva. Se non si ha un account, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > È necessaria una sottoscrizione di Azure con le autorizzazioni per la creazione di entità servizio. il **ruolo di proprietario** lo fornisce. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account per concedere le autorizzazioni appropriate.
* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo. Assicurarsi che sia presente l'[estensione Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Leggere l' [EFLOW](https://aka.ms/AzEFLOW-docs).

## <a name="deployment-steps"></a>Passaggi di distribuzione

Di seguito viene illustrato il flusso generale del documento e in 5 semplici passaggi è necessario configurare tutti per l'esecuzione di analisi video in tempo reale in un dispositivo Windows con EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagramma IoT Edge per Linux in Windows (EFLOW)":::

1. [Installare EFLOW](https://aka.ms/AzEFLOW-install) nel dispositivo Windows. 

    1. Se si usa il PC Windows, nella pagina iniziale dell'interfaccia di [amministrazione di Windows](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) , sotto l'elenco delle connessioni, viene visualizzata una connessione host locale che rappresenta il PC in cui è in esecuzione l'interfaccia di amministrazione di Windows. 
    1. Qui vengono visualizzati anche eventuali server, PC o cluster aggiuntivi gestiti.
    1. È possibile usare l'interfaccia di amministrazione di Windows per installare e gestire Azure EFLOW nel dispositivo locale o nei dispositivi gestiti in remoto. In questa guida la connessione host locale è stata utilizzata come dispositivo di destinazione per la distribuzione di Azure IoT Edge per Linux in Windows. Di conseguenza, viene visualizzato il localhost anche elencato come dispositivo IoT Edge.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Passaggi per le distribuzioni: centro di amministrazione di Windows":::
1. Fare clic sul dispositivo IoT Edge per connetterlo. verrà visualizzata una scheda Panoramica e shell comandi. La scheda shell comandi consente di inviare comandi al dispositivo perimetrale.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Passaggi per le distribuzioni-gestione Azure IoT Edge":::
1. Passare alla shell dei comandi e digitare il comando seguente:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Il modulo Live Video Analytics viene eseguito sul dispositivo perimetrale con [account utente locali](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)senza privilegi. Sono inoltre [necessarie alcune cartelle locali](deploy-iot-edge-device.md#granting-permissions-to-device-storage) per l'archiviazione dei dati di configurazione dell'applicazione. Infine, per questa guida è possibile sfruttare un [simulatore RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) che inoltra un feed video in tempo reale al modulo LVA per l'analisi. Questo simulatore accetta come input i file video pre-registrati da una directory di input. 
    
    Lo script Prep-device usato sopra consente di automatizzare queste attività, in modo da poter eseguire un comando e avere tutte le cartelle di input e configurazione pertinenti, i file di input video e gli account utente con privilegi creati senza problemi. Una volta che il comando è stato completato correttamente, verranno visualizzate le cartelle seguenti create sul dispositivo perimetrale. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Si notino i file video (*. MKV) nella cartella/Home/lvaedgeuser/Samples/input, che vengono usati come file di input da analizzare. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Analisi":::
1. Ora che il dispositivo Edge è stato configurato, registrato nell'hub ed eseguito correttamente con le strutture di cartelle corrette create, il passaggio successivo consiste nel configurare le risorse di Azure aggiuntive seguenti e distribuire il modulo LVA. 

    * Account di archiviazione
    * Account Servizi multimediali di Azure

    A tale proposito, è consigliabile usare lo [script di configurazione delle risorse di analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse necessarie nella sottoscrizione di Azure. A questo scopo, attenersi alla procedura seguente:

    1. Aprire [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Se si usa Cloud Shell per la prima volta, verrà chiesto di selezionare una sottoscrizione, oltre che di creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare **Crea risorsa di archiviazione** per creare un account di archiviazione per le informazioni della sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
    1. Nel menu a discesa sul lato sinistro della finestra di Cloud Shell selezionare Bash come ambiente.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Ambiente bash":::
    1. Eseguire il comando seguente.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Assicurarsi di scegliere **Y** quando viene richiesto di scegliere il proprio dispositivo perimetrale come dispositivo IOT Edge, perché in precedenza sono stati creati il dispositivo e l'hub Internet. Verranno anche richiesti il nome dell'hub Internet e l'ID del dispositivo IoT Edge. È possibile accedere al portale di Azure e fare clic sull'hub Internet e quindi passare all'opzione IoT Edge nel pannello del portale dell'hub Internet.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Vedere dispositivi IoT Edge":::

    Al termine, è possibile accedere nuovamente alla shell dei comandi del dispositivo IoT Edge ed eseguire il comando seguente.
    
    `sudo iotedge list`
    
    Verranno visualizzati i quattro moduli seguenti distribuiti ed eseguiti sul dispositivo perimetrale. Si noti che lo script di creazione di risorse distribuisce il modulo LVA insieme a moduli di IoT Edge (edgeAgent e edgeHub) e a un modulo del simulatore RTSP per fornire il feed video RTSP simulato.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Vedere lo stato":::
1. Con i moduli distribuiti e configurati, è possibile eseguire il primo grafico multimediale LVA in EFLOW. È possibile eseguire un semplice grafico di rilevamento movimento come riportato di seguito e visualizzare i risultati eseguendo i passaggi seguenti:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Grafico rilevamento movimento":::

    1. [Configurare](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) l'estensione strumenti di Azure.
    
        > [!Note]
        > Utilizzare il seguente percorso: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Impostare la topologia, creare un'istanza di un grafico e attivarla tramite queste [chiamate al metodo diretto](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Osservare i risultati](get-started-detect-motion-emit-events-quickstart.md#observe-results) nell'hub.
    1. Richiama i [metodi di pulizia](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate).
    1. Se non è necessario, eliminare le risorse.

        > [!IMPORTANT]
        > Le risorse non eliminate possono essere ancora attive e sostenere costi di Azure.
    
## <a name="next-steps"></a>Passaggi successivi

* Provare il rilevamento del movimento insieme alla registrazione dei video pertinenti nel cloud. Seguire i passaggi della Guida introduttiva [rilevare il movimento, registrare i clip video in servizi multimediali](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) .
* Esegui [intelligenza artificiale su video live](use-your-model-quickstart.md#overview) (è possibile ignorare la configurazione dei prerequisiti perché è già stata eseguita in precedenza)
* Usare l' [estensione vs code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) per visualizzare altri grafici multimediali.
* Usare una [fotocamera IP](https://en.wikipedia.org/wiki/IP_camera)  che supporta RTSP invece di usare il simulatore RTSP. È possibile trovare videocamere IP che supportano RTSP nella pagina dei [prodotti conformi a ONVIF](https://www.onvif.org/conformant-products/). Cercare dispositivi conformi ai profili G, S o T.

