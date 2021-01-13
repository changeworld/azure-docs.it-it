---
ms.openlocfilehash: eaa44ae9d8bf4d723944da695d9a063937b7e020
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2021
ms.locfileid: "98061034"
---
Quando si configurano le risorse di Azure, nella VM Linux di Azure usata come dispositivo IoT Edge viene copiato un breve video del traffico in autostrada. In questa guida il file video viene usato per simulare uno streaming live.

Aprire un'applicazione, ad esempio il [lettore multimediale VLC](https://www.videolan.org/vlc/). Premere CTRL+N e quindi incollare un collegamento al [video di esempio relativo allo svincolo autostradale](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) per avviare la riproduzione. Viene visualizzato il filmato di molti veicoli in transito nel traffico di un'autostrada.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

In questo argomento di avvio rapido si userà Analisi video live in IoT Edge per rilevare oggetti come veicoli e persone. Gli eventi di inferenza associati verranno pubblicati nell'hub di IoT Edge.
