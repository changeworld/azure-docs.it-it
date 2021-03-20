---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821520"
---
La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari GStreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Gli sviluppatori devono installare diverse dipendenze e plug-in, vedere [installazione in Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) o [installazione in Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). I file binari GStreamer devono trovarsi nel percorso di sistema, in modo che l'SDK di riconoscimento vocale possa caricare i file binari durante il Runtime. Ad esempio, in Windows, se l'SDK di riconoscimento vocale è in grado di trovarsi `libgstreamer-1.0-0.dll` durante il runtime, significa che i file binari GStreamer si trovano nel percorso di sistema.

