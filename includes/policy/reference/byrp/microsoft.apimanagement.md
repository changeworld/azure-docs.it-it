---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f9a714936d0851d31a7b04001bd4fc4cc08c36d7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500388"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management servizio deve usare uno SKU che supporta le reti virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Con gli SKU supportati API Management, la distribuzione del servizio in una rete virtuale sblocca funzionalità avanzate di rete e sicurezza API Management che consentono un maggiore controllo sulla configurazione della sicurezza di rete. Per altre informazioni, vedere [https://aka.ms/apimvnet](https://aka.ms/apimvnet). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[I servizi di gestione API devono usare una rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |La distribuzione di Rete virtuale di Azure offre sicurezza avanzata e isolamento e consente di posizionare il servizio API Management in una rete instradabile non Internet a cui si controlla l'accesso. Queste reti possono quindi essere connesse alle reti locali usando varie tecnologie VPN, che consentono l'accesso ai servizi back-end all'interno della rete e/o in locale. Il portale per sviluppatori e il gateway dell'API possono essere configurati in modo che siano accessibili da Internet o solo all'interno della rete virtuale. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
