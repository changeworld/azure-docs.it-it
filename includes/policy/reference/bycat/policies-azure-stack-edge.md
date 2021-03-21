---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 62f5e60fb6949b2f65760713b62046642862ee9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593587"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I dispositivi Azure Stack Edge devono usare la doppia crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Per proteggere i dati inattivi nel dispositivo, assicurarsi che venga applicata la doppia crittografia, che l'accesso ai dati sia controllato e che, quando il dispositivo viene disattivato, i dati vengano cancellati in modo sicuro dai dischi dati. La doppia crittografia prevede l'uso di due livelli di crittografia: la crittografia BitLocker XTS-AES a 256 bit sui volumi di dati e la crittografia incorporata dei dischi rigidi. Per altre informazioni, vedere la documentazione sulla panoramica della sicurezza per il dispositivo Stack Edge specifico. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
