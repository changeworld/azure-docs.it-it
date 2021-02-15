---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fc2bdd31f0dd2649c4f8a7a38e161f04f0e80260
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100268"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I domini di Griglia di eventi di Azure devono usare collegamenti privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma del collegamento privato gestisce la connettività tra il consumer e i servizi tramite la rete backbone di Azure. Il mapping degli endpoint privati ai soli domini di Griglia di eventi anziché all'intero servizio, garantisce la protezione dai rischi di perdita dei dati. Per altre informazioni, vedere: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Gli argomenti di Griglia di eventi di Azure devono usare collegamenti privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma del collegamento privato gestisce la connettività tra il consumer e i servizi tramite la rete backbone di Azure. Il mapping degli endpoint privati ai soli argomenti anziché all'intero servizio, garantisce la protezione dai rischi di perdita dei dati. Per altre informazioni, vedere [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
