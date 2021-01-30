---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214105"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Configurare il provider di impronte digitali dei sensori

Per iniziare, creare e configurare un provider di impronte digitali di sensori. Il provider di impronte digitali dei sensori leggerà i sensori specifici della piattaforma nel dispositivo e convertirà queste letture in una rappresentazione comune utilizzata dalla sessione di ancoraggi nello spazio nel cloud.

> [!IMPORTANT]
> Assicurarsi [di controllare](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) se i sensori abilitati sono disponibili sulla piattaforma.