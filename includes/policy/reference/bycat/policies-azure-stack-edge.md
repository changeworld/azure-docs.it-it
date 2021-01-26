---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f99f86606ea2f186eeeac9f14d621488c95a9c18
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806053"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I dispositivi Azure Stack Edge devono usare la doppia crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Per proteggere i dati inattivi nel dispositivo, assicurarsi che venga applicata la doppia crittografia, che l'accesso ai dati sia controllato e che, quando il dispositivo viene disattivato, i dati vengano cancellati in modo sicuro dai dischi dati. La doppia crittografia prevede l'uso di due livelli di crittografia: la crittografia BitLocker XTS-AES a 256 bit sui volumi di dati e la crittografia incorporata dei dischi rigidi. Per altre informazioni, vedere la documentazione sulla panoramica della sicurezza per il dispositivo Stack Edge specifico. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
