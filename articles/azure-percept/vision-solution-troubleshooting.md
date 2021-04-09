---
title: Risolvere i problemi relativi ai moduli visione e visione di Azure Percept
description: Suggerimenti per la risoluzione dei problemi relativi ad alcuni dei problemi più comuni rilevati nella visione di prototipi di intelligenza artificiale
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663516"
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

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Pagina Edge per il dispositivo selezionato che mostra il contenuto della scheda moduli." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eliminare un dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Fare clic sull'icona dell' **Hub** Internet.

1. Selezionare l'hub delle cose a cui è connesso il dispositivo di destinazione.

1. Selezionare **IOT Edge** e selezionare la casella accanto all'ID del dispositivo di destinazione. Fare clic sull'icona del cestino per eliminare il dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Icona Elimina evidenziata nella Home page di Internet Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi del modulo Eye

Se si verifica un problema con **WebStreamModule**, assicurarsi che **azureeyemodule**, che esegue l'inferenza del modello di visione, sia in esecuzione. Per controllare lo stato di runtime, passare alla [portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e passare a **tutte le risorse**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Fare clic sulla scheda **moduli** per visualizzare lo stato di runtime di tutti i moduli installati.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Schermata di stato di runtime del modulo del dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Se lo stato di runtime di **azureeyemodule** non è elencato come **in esecuzione**, fare clic su **imposta moduli**  ->  **azureeyemodule**. Nella pagina **Impostazioni modulo** impostare **stato desiderato** su **in esecuzione** e fare clic su **Aggiorna**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Schermata di configurazione dell'impostazione del modulo.":::

## <a name="view-device-rtsp-video-stream"></a>Visualizzazione del flusso video RTSP del dispositivo

Visualizzare il flusso video RTSP del dispositivo in [Azure Percept Studio](./how-to-view-video-stream.md) o [VLC Media Player](https://www.videolan.org/vlc/index.html).

Per aprire il flusso RTSP in VLC Media Player, passare a **media**  ->  **Open Network Stream**  ->  **RTSP://[Device IP address]/result**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di Azure Percept DK, vedere la [Guida generale alla risoluzione dei problemi](./troubleshoot-dev-kit.md) .