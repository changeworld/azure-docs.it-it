---
title: Risolvere i problemi relativi ai moduli visione e visione di Azure Percept
description: Suggerimenti per la risoluzione dei problemi relativi ad alcuni dei problemi più comuni rilevati nella visione di prototipi di intelligenza artificiale
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074693"
---
# <a name="vision-solution-troubleshooting"></a>Risoluzione dei problemi della soluzione

Vedere le linee guida seguenti per informazioni sulla risoluzione dei problemi relativi a soluzioni senza codice per la visione artificiale in Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Eliminare un progetto di visione artificiale

1. Passare a https://www.customvision.ai/projects.

1. Passare il puntatore del mouse sul progetto che si desidera eliminare e fare clic sull'icona del cestino per eliminare il progetto.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Pagina Progetti in Visione personalizzata con icona Elimina evidenziata.":::

## <a name="check-which-modules-are-on-a-device"></a>Controllare quali moduli si trovano in un dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Fare clic sull'icona dell' **Hub** Internet.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Portale di Azure Home page con l'icona dell'hub cose evidenziata." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Selezionare l'hub delle cose a cui è connesso il dispositivo di destinazione.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Elenco di hub Internet.":::

1. Selezionare **IOT Edge** e fare clic sul dispositivo nella scheda **ID dispositivo** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge Home page.":::

1. I moduli del dispositivo verranno elencati nella scheda **moduli** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="IoT Edge pagina per il dispositivo selezionato che mostra il contenuto della scheda moduli." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eliminare un dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Fare clic sull'icona dell' **Hub** Internet.

1. Selezionare l'hub delle cose a cui è connesso il dispositivo di destinazione.

1. Selezionare **IOT Edge** e selezionare la casella accanto all'ID del dispositivo di destinazione. Fare clic sull'icona del cestino per eliminare il dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Icona Elimina evidenziata in IoT Edge Home page.":::

## <a name="eye-module-troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi del modulo Eye

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Verificare lo stato di runtime di azureeyemodule

Se si verifica un problema con **WebStreamModule**, verificare che **azureeyemodule**, che gestisce l'inferenza del modello di visione, sia in esecuzione. Per controllare lo stato di runtime, passare alla [portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e passare a **tutte le risorse**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Fare clic sulla scheda **moduli** per visualizzare lo stato di runtime di tutti i moduli installati.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Schermata di stato di runtime del modulo del dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Se lo stato di runtime di **azureeyemodule** non è elencato come **in esecuzione**, fare clic su **imposta moduli**  ->  **azureeyemodule**. Nella pagina **Impostazioni modulo** impostare **stato desiderato** su **in esecuzione** e fare clic su **Aggiorna**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Schermata di configurazione dell'impostazione del modulo.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>Aggiornare TelemetryIntervalNeuralNetworkMs

Se si verifica l'errore di limitazione dei conteggi seguente, sarà necessario aggiornare il valore TelemetryIntervalNeuralNetworkMs nelle impostazioni del dispositivo gemello del modulo azureeyemodule.

|Messaggio di errore|
|------|
|Il numero totale di messaggi in IotHub ' xxxxxxxxx ' ha superato la quota allocata. Numero massimo di messaggi consentiti: "8000", numero di messaggi corrente: "xxxx". Le operazioni di invio e ricezione sono bloccate per questo hub fino al giorno UTC successivo. Prendere in considerazione la possibilità di aumentare le unità per questo hub per aumentare la quota.|

TelemetryIntervalNeuralNetworkMs determina la frequenza di invio dei messaggi (in millisecondi) dalla rete neurale. Le sottoscrizioni di Azure hanno un numero limitato di messaggi al giorno, a seconda del livello di sottoscrizione. Se ci si trova bloccati perché sono stati inviati troppi messaggi, aumentare questo valore a un numero più alto. 12000 (vale a dire una volta ogni 12 secondi) fornirà un bel round 7200 di messaggi al giorno, che è al di sotto del limite del messaggio 8000 per la sottoscrizione gratuita.

Per aggiornare il valore di TelemetryIntervalNeuralNetworkMs, seguire questa procedura:

1. Accedere al [portale di Azure](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) e aprire tutte le **risorse**.

1. Nella pagina **tutte le risorse** fare clic sul nome dell'hub delle cose di cui è stato effettuato il provisioning in DevKit durante l'installazione.

1. Sul lato sinistro della pagina hub Internet, fare clic su **IOT Edge** in **gestione automatica dei dispositivi**. Nella pagina dispositivi IoT Edge individuare l'ID del dispositivo devkit. Fare clic sull'ID del dispositivo DevKit per aprire la relativa pagina del dispositivo IoT Edge.

1. Selezionare **azureeyemodule** nella scheda **moduli** .

1. Nella pagina azureeyemodule aprire **Module Identity gemelle**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Screenshot della pagina del modulo." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Scorrere fino a **Proprietà**. Si noti che le proprietà "Running" e "Logging" non sono attive in questo momento.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Screenshot delle proprietà del modulo gemello." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Aggiornare il valore di **TelemetryIntervalNeuralNetworkMs** come desiderato e fare clic sull'icona **Salva** .

## <a name="view-device-rtsp-video-stream"></a>Visualizzazione del flusso video RTSP del dispositivo

Visualizzare il flusso video RTSP del dispositivo in [Azure Percept Studio](./how-to-view-video-stream.md) o [VLC Media Player](https://www.videolan.org/vlc/index.html).

Per aprire il flusso RTSP in VLC Media Player, passare a **media**  ->  **Open Network Stream**  ->  **RTSP://[Device IP address]/result**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di Azure Percept DK, vedere la [Guida generale alla risoluzione dei problemi](./troubleshoot-dev-kit.md) .