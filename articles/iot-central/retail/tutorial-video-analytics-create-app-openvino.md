---
title: Esercitazione - Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (OpenVINO)
description: Questa esercitazione illustra come creare un'applicazione di analisi video in IoT Central. È possibile crearla, personalizzarla e connetterla ad altri servizi di Azure. Questa esercitazione usa il toolkit Intel OpenVINO&trade; per il rilevamento oggetti in tempo reale.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: a201a0300cb4ae0fba1a41b5f64838c17904fa83
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832097"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (OpenVINO&trade;)

Questa esercitazione illustra agli sviluppatori di soluzioni come creare un'applicazione di analisi video con il modello di applicazione *Analisi video - rilevamento movimento e oggetti* di IoT Central, dispositivi Azure IoT Edge, Servizi multimediali di Azure e il sistema di rilevamento movimento e oggetti OpenVINO&trade; ottimizzato per l'hardware Intel. La soluzione usa un punto vendita al dettaglio per mostrare come soddisfare l'esigenza aziendale comune di monitorare le telecamere di sicurezza. La soluzione usa il rilevamento automatico degli oggetti in un feed video per identificare e individuare rapidamente gli eventi interessanti.

> [!TIP]
> Per usare YOLO v3 invece di OpenVINO&trade; per il rilevamento movimento e oggetti, vedere [Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt): questo file consente di registrare le varie opzioni di configurazione necessarie durante l'utilizzo di queste esercitazioni.
- [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json): è necessario scaricare questo file solo se si prevede di usare il dispositivo Intel NUC nella seconda esercitazione.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Modificare il manifesto della distribuzione

La distribuzione di un modulo IoT Edge viene eseguita usando un manifesto della distribuzione. In IoT Central è possibile importare il manifesto come modello di dispositivo e quindi lasciare che IoT Central gestisca la distribuzione del modulo.

Per preparare il manifesto della distribuzione:

1. Aprire il file *deployment.openvino.amd64.json* salvato nella cartella *lva-configuration* usando un editor di testo.

1. Trovare le impostazioni di `LvaEdgeGatewayModule` e verificare che il nome dell'immagine sia quello illustrato nel frammento di codice seguente:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Aggiungere il nome dell'account di Servizi multimediali nel nodo `env` nella sezione `LvaEdgeGatewayModule`. È stato annotato il nome dell'account di Servizi multimediali nel file *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. Il modello non espone queste proprietà in IoT Central, quindi è necessario aggiungere i valori di configurazione di Servizi multimediali nel manifesto della distribuzione. Individuare il modulo `lvaEdge` e sostituire i segnaposto con i valori annotati nel file *scratchpad.txt* quando è stato creato l'account di Servizi multimediali.

    `azureMediaServicesArmId` è il valore di **ID risorsa** annotato nel file *scratchpad.txt* quando è stato creato l'account di Servizi multimediali.

    La tabella seguente mostra i valori dell'**API Connettersi a Servizi multimediali (JSON)** nel file *scratchpad.txt* da usare nel manifesto della distribuzione:

    | Manifesto della distribuzione       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Usare la tabella precedente per assicurarsi di aggiungere i valori corretti nel manifesto di distribuzione. In caso contrario, il dispositivo non funzionerà.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Salvare le modifiche.

Questa esercitazione configura la soluzione per l'uso del modulo OpenVINO&trade; per il rilevamento movimento e oggetti. Il frammento di codice seguente mostra la configurazione del modulo:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Sostituire il manifesto

Nella pagina **LVA Edge Gateway v2** selezionare **+ Sostituisci manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Sostituire il manifesto":::

Passare alla cartella *lva-configuration* e selezionare il file manifesto *deployment.openvino.amd64.json* modificato in precedenza. Selezionare **Carica**. Dopo aver completato la convalida, selezionare **Sostituisci**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'applicazione non è più necessaria, è possibile rimuovere tutte le risorse create come indicato di seguito:

1. Nell'applicazione IoT Central passare alla pagina **Applicazione** nella sezione **Amministrazione**. Selezionare **Elimina**.
1. Nel portale di Azure eliminare il gruppo di risorse **lva-rg**.
1. Nel computer locale arrestare il contenitore Docker **amp-viewer**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stata creata un'applicazione IoT Central usando il modello di applicazione **Analisi video - rilevamento movimento e oggetti**, è stato creato un modello di dispositivo per il dispositivo gateway ed è stato aggiunto un dispositivo gateway all'applicazione.

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in una macchina virtuale cloud con flussi video simulati:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (macchina virtuale Linux)](tutorial-video-analytics-iot-edge-vm.md)

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in un dispositivo reale con la telecamera **ONVIF**:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)