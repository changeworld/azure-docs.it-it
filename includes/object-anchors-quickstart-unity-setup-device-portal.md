---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044687"
---
### <a name="set-up-the-windows-device-portal"></a>Configurare il portale del dispositivo Windows

Per connettersi alla HoloLens tramite WiFi, seguire questa procedura:

1. Prima [di tutto, connettere la HoloLens al Wi-Fi](https://docs.microsoft.com/hololens/hololens-network).

2. Ottenere quindi l'indirizzo IP sul dispositivo in **impostazioni > rete & Internet > Wi-Fi > opzioni avanzate**.

3. Da un Web browser nel PC passa a `https://<YOUR_HOLOLENS_IP_ADDRESS>` Il browser visualizzerà il messaggio seguente: "Si è verificato un problema con il certificato di sicurezza del sito Web". Questo messaggio viene visualizzato perché il certificato rilasciato al portale del dispositivo è un certificato autofirmato. È possibile ignorare l'errore del certificato e continuare.

Per altre informazioni sulla configurazione del portale per dispositivi Windows, vedere [qui](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal) .
