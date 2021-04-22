---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d72d4493b39d959c5429ed2ca9f5883831b9372a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862733"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il gruppo di contenitori dell'istanza di Azure Container deve essere distribuito in una rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Proteggere la comunicazione tra i contenitori con reti virtuali di Azure. Quando si specifica una rete virtuale, le risorse all'interno della rete virtuale possono comunicare in modo sicuro e privato tra loro. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Il gruppo di contenitori di Istanza di Azure Container deve usare la chiave gestita dal cliente per la crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Proteggere i contenitori con maggiore flessibilità usando chiavi gestite dal cliente. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. L'uso di chiavi gestite dal cliente offre funzionalità aggiuntive per controllare la rotazione della chiave di crittografia della chiave o cancellare i dati crittograficamente. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
