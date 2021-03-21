---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582339"
---
Per i dati in corso:

- Il protocollo TLS 1,2 standard viene usato per i dati che si spostano tra il dispositivo e Azure. Non esiste alcun fallback a TLS 1,1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1,2 non è supportato. TLS 1,2 è necessario anche per la gestione del portale e dell'SDK.
- Quando i client accedono al dispositivo tramite l'interfaccia utente Web locale di un browser, viene usato standard TLS 1,2 come protocollo di sicurezza predefinito.

    - La procedura consigliata consiste nel configurare il browser per l'uso di TLS 1,2.
    - Se il browser non supporta TLS 1,2, è possibile usare TLS 1,1 o TLS 1,0.
- Si consiglia di usare SMB 3,0 con la crittografia per proteggere i dati quando lo si copia dai server dati.
