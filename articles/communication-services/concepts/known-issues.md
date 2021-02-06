---
title: Servizi di comunicazione di Azure-problemi noti
description: Informazioni sui problemi noti relativi ai servizi di comunicazione di Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628145"
---
# <a name="known-issues-azure-communication-services"></a>Problemi noti: Servizi di comunicazione di Azure

Questo articolo fornisce informazioni sui problemi noti associati ai servizi di comunicazione di Azure.

## <a name="video-streaming-quality-on-chromeandroid"></a>Qualità dello streaming video in Chrome/Android 

Le prestazioni di streaming video possono essere ridotte in Android Chrome.

### <a name="possible-causes"></a>Possibili cause
La qualità dei flussi remoti dipende dalla risoluzione del renderer sul lato client usato per avviare il flusso. Quando si sottoscrive un flusso remoto, un ricevitore determinerà una propria risoluzione in base alle dimensioni del renderer sul lato client del mittente.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>I microfoni della cuffia Bluetooth non sono stati rilevati

È possibile che si verifichino problemi durante la connessione della cuffia Bluetooth a una chiamata ai servizi di comunicazione.

### <a name="possible-causes"></a>Possibili cause
Non è disponibile un'opzione per selezionare il microfono Bluetooth in iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Il cambio continuo dei dispositivi video potrebbe causare l'arresto temporaneo dello streaming video

Il cambio tra dispositivi video può causare la sospensione del flusso video mentre il flusso viene acquisito dal dispositivo selezionato.

### <a name="possible-causes"></a>Possibili cause
Lo streaming e il cambio tra dispositivi multimediali sono a elevato utilizzo di calcolo. Il cambio frequente può causare un calo delle prestazioni. Gli sviluppatori sono invitati a arrestare un flusso di dispositivi prima di avviarne un altro.
