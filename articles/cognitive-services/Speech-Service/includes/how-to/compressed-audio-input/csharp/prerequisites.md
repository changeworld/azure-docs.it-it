---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417737"
---
La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari GStreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Gli sviluppatori devono installare diverse dipendenze e plug-in, vedere [installazione in Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) o [installazione in Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). I file binari GStreamer devono trovarsi nel percorso di sistema, in modo che l'SDK di riconoscimento vocale possa caricare i file binari durante il Runtime. Ad esempio, in Windows, se l'SDK di riconoscimento vocale è in grado di trovare `libgstreamer-1.0-0.dll` o `gstreamer-1.0-0.dll` (per la versione più recente di gstreamer) durante la fase di esecuzione, significa che i file binari GStreamer si trovano nel percorso di sistema.

