---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bd4990fe25c60f09ecc44a12e8ca000d4facb03e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555455"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Configurazione app deve usare una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Le chiavi gestite dal cliente offrono protezione dati avanzata consentendo di gestire le chiavi di crittografia. Tale funzionalità è spesso necessaria per soddisfare i requisiti di conformità. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[Configurazione app deve usare un collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma del collegamento privato gestisce la connettività tra il consumer e i servizi tramite la rete backbone di Azure. Il mapping degli endpoint privati alle sole istanze di Configurazione app anziché all'intero servizio, garantisce la protezione dai rischi di perdita dei dati. Per altre informazioni, vedere [https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint). |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
