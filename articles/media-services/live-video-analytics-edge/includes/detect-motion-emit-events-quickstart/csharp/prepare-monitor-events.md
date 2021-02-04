---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531668"
---
Si userà il modulo di Analisi video live in IoT Edge per rilevare il movimento nel flusso video live in ingresso e inviare eventi all'hub IoT. Per visualizzare gli eventi, seguire questa procedura:

1. Aprire il riquadro Explorer in Visual Studio Code e cercare Hub IoT di Azure nell'angolo in basso a sinistra.
1. Espandere il nodo **Dispositivi**.
1. Fare clic con il pulsante destro del mouse sul file **lva-sample-device** e scegliere **Avvia il monitoraggio endpoint eventi predefinito**.

    ![Avvia monitoraggio endpoint eventi predefinito](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Potrebbe essere richiesto di fornire informazioni di endpoint predefinite per l'hub Internet delle cose. Per ottenere tali informazioni, in portale di Azure passare all'hub Internet e cercare l'opzione **endpoint predefiniti** nel riquadro di spostamento a sinistra. Fare clic qui e cercare l' **endpoint compatibile con l'hub eventi** nella sezione **endpoint compatibile con hub eventi** . Copiare e usare il testo nella casella. L'endpoint sarà simile al seguente:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
