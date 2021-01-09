---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050910"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I domini di griglia di eventi di Azure devono usare il collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Il collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma di collegamento privato gestisce la connettività tra il consumer e i servizi tramite la rete backbone di Azure. Eseguendo il mapping degli endpoint privati ai domini di griglia di eventi anziché all'intero servizio, sarà anche possibile proteggersi da rischi di perdita dei dati. Per ulteriori informazioni, vedere: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Gli argomenti di griglia di eventi di Azure devono usare il collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Il collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma di collegamento privato gestisce la connettività tra il consumer e i servizi tramite la rete backbone di Azure. Eseguendo il mapping degli endpoint privati agli argomenti anziché dell'intero servizio, sarà anche possibile proteggersi da rischi di perdita dei dati. Per ulteriori informazioni, vedere: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
