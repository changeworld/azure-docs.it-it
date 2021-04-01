---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750357"
---
Nel passaggio di [generazione e distribuzione del manifesto della distribuzione di IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) in Visual Studio Code espandere il nodo **lva-sample-device** in **HUB IOT DI AZURE** (sezione in basso a sinistra). Si noterà che sono distribuiti i moduli seguenti:

* Il modulo Analisi video live, denominato `lvaEdge`
* Il modulo `rtspsim`, che simula un server RTSP che funge da origine di un feed di video live

  ![Moduli](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> I passaggi precedenti presuppongono che si stia usando la macchina virtuale creata dallo script di installazione. Se invece si usa il proprio dispositivo perimetrale, passare al dispositivo perimetrale ed eseguire i comandi seguenti con **diritti di amministratore** per estrarre e archiviare il file video di esempio usato per questa Guida introduttiva:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
