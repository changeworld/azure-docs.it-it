---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956299"
---
Nel passaggio di [generazione e distribuzione del manifesto della distribuzione di IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) in Visual Studio Code espandere il nodo **lva-sample-device** in **HUB IOT DI AZURE** (sezione in basso a sinistra). Si noterà che sono distribuiti i moduli seguenti:

* Il modulo Analisi video live, denominato `lvaEdge`
* Il modulo `rtspsim`, che simula un server RTSP che funge da origine di un feed di video live

  ![Moduli](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> I passaggi precedenti presuppongono che si stia usando la macchina virtuale creata dallo script di installazione. Se invece si usa il proprio dispositivo perimetrale, passare al dispositivo perimetrale ed eseguire i comandi seguenti con **diritti di amministratore** per estrarre e archiviare il file video di esempio usato per questa Guida introduttiva:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
