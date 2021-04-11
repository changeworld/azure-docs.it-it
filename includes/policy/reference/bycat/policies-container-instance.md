---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8ca15686659a7fb9bd1390df7d9af041805df7ee
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091001"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il gruppo di contenitori di istanze di contenitore di Azure deve essere distribuito in una rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Proteggere le comunicazioni tra i contenitori con le reti virtuali di Azure. Quando si specifica una rete virtuale, le risorse all'interno della rete virtuale possono comunicare in modo sicuro e privato tra loro. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Il gruppo di contenitori di istanze di contenitore di Azure deve usare la chiave gestita dal cliente per la crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Proteggi i tuoi contenitori con maggiore flessibilità usando chiavi gestite dal cliente. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. L'uso delle chiavi gestite dal cliente fornisce funzionalità aggiuntive per controllare la rotazione della chiave di crittografia della chiave o la cancellazione crittografica dei dati. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
