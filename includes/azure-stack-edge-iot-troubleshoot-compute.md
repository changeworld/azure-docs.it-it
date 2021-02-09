---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831545"
---
Usare le risposte di runtime dell'agente IoT Edge per risolvere gli errori correlati al calcolo. Ecco un elenco di risposte possibili:

* 200 - OK
* 400 - La configurazione della distribuzione è in formato non corretto o non valida.
* 417: il dispositivo non ha un set di configurazione della distribuzione.
* 412 - La versione dello schema nella configurazione della distribuzione non è valida.
* 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
* 500 - Si è verificato un errore nel runtime di IoT Edge.

Per ulteriori informazioni, vedere [IOT Edge Agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

Il seguente errore è correlato al servizio IoT Edge in Azure Stack Edge Pro<!--/ Data Box Gateway--> dispositivo.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>I moduli di calcolo presentano uno stato sconosciuto e non possono essere usati

#### <a name="error-description"></a>Descrizione errore

Tutti i moduli del dispositivo mostrano lo stato sconosciuto e non possono essere usati. Lo stato sconosciuto viene mantenuto in un riavvio.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Soluzione suggerita

Eliminare il servizio IoT Edge e quindi ridistribuire i moduli. Per ulteriori informazioni, vedere [Remove IOT Edge service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
