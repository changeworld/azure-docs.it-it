---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761546"
---
Usare le risposte di runtime dell'agente IoT Edge per risolvere gli errori correlati al calcolo. Ecco un elenco di risposte possibili:

* 200 - OK
* 400 - La configurazione della distribuzione è in formato non corretto o non valida.
* 417: il dispositivo non ha un set di configurazione della distribuzione.
* 412 - La versione dello schema nella configurazione della distribuzione non è valida.
* 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
* 500 - Si è verificato un errore nel runtime di IoT Edge.

Per ulteriori informazioni, vedere [IOT Edge Agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).