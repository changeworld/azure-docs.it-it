---
title: Servizi di comunicazione di Azure-Domande frequenti/problemi noti
description: Scopri di più sui servizi di comunicazione di Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655888"
---
# <a name="faq--known-issues"></a>Domande frequenti/problemi noti
Questo articolo fornisce informazioni sui problemi noti e le domande frequenti relative ai servizi di comunicazione di Azure.

## <a name="faq"></a>Domande frequenti

### <a name="why-is-the-quality-of-my-video-degraded"></a>Perché la qualità del video è peggiorata?

La qualità dei flussi video è determinata dalla dimensione del renderer sul lato client utilizzato per avviare il flusso. Quando si sottoscrive un flusso remoto, un ricevitore determinerà una propria risoluzione in base alle dimensioni del renderer sul lato client del mittente.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Perché non è possibile enumerare/selezionare i dispositivi mic/speaker in Safari?

Le applicazioni non possono enumerare/selezionare i dispositivi mic/speaker (ad esempio Bluetooth) su Safari iOS/iPad. Si tratta di una limitazione del sistema operativo: è sempre presente un solo dispositivo.

Per Safari in MacOS, l'app non può enumerare/selezionare il relatore tramite i servizi di comunicazione Gestione dispositivi, che devono essere selezionati tramite il sistema operativo. Se si usa Chrome in MacOS, l'app può enumerare/selezionare i dispositivi tramite i servizi di comunicazione Gestione dispositivi.

## <a name="known-issues"></a>Problemi noti

In questa sezione vengono fornite informazioni sui problemi noti associati ai servizi di comunicazione di Azure.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Il cambio continuo dei dispositivi video potrebbe causare l'arresto temporaneo dello streaming video

Il cambio tra dispositivi video può causare la sospensione del flusso video mentre il flusso viene acquisito dal dispositivo selezionato.

#### <a name="possible-causes"></a>Possibili cause
Lo streaming e il cambio tra dispositivi multimediali sono a elevato utilizzo di calcolo. Il cambio frequente può causare un calo delle prestazioni. Gli sviluppatori sono invitati a arrestare un flusso di dispositivi prima di avviarne un altro.
