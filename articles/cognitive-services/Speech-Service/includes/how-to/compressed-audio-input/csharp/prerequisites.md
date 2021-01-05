---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821554"
---
La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari GStreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Gli sviluppatori devono installare diverse dipendenze e plug-in, vedere [installazione in Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) o [installazione in Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). I file binari GStreamer devono trovarsi nel percorso di sistema, in modo che l'SDK di riconoscimento vocale possa caricare i file binari durante il Runtime. Ad esempio, in Windows, se l'SDK di riconoscimento vocale è in grado di trovarsi `libgstreamer-1.0-0.dll` durante il runtime, significa che i file binari GStreamer si trovano nel percorso di sistema.

