---
ms.openlocfilehash: 3196d377c09ca0cce24093710bc4be13fb21d2e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99532104"
---
Assicurarsi di aver completato i passaggi descritti in [Preparare il monitoraggio degli eventi](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Avvia monitoraggio endpoint eventi predefinito](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Potrebbe essere richiesto di fornire informazioni di endpoint predefinite per l'hub Internet delle cose. Per ottenere tali informazioni, in portale di Azure passare all'hub Internet e cercare l'opzione **endpoint predefiniti** nel riquadro di spostamento a sinistra. Fare clic qui e cercare l' **endpoint compatibile con l'hub eventi** nella sezione **endpoint compatibile con hub eventi** . Copiare e usare il testo nella casella. L'endpoint sarà simile al seguente:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
