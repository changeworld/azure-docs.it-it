---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102517565"
---
1. Prima di iniziare, verificare che:

    1. È stato configurato e [attivato il dispositivo Azure stack Edge Pro](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) con una risorsa Azure stack Edge in Azure.
    1. [Il calcolo è stato configurato in questo dispositivo nel portale di Azure](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Connettersi all'interfaccia di PowerShell](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Usare il comando seguente per abilitare i Management Pack nel dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```