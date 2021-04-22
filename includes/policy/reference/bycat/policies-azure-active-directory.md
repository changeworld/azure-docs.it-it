---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866633"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services domini gestiti devono usare solo la modalità TLS 1.2](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Usare la modalità solo TLS 1.2 per i domini gestiti. Per impostazione predefinita, Azure AD Domain Services l'uso di crittografia, ad esempio NTLM v1 e TLS v1. Queste crittografia possono essere necessarie per alcune applicazioni legacy, ma sono considerate deboli e possono essere disabilitate se non sono necessarie. Quando la modalità solo TLS 1.2 è abilitata, qualsiasi client che effettua una richiesta che non usa TLS 1.2 avrà esito negativo. Per altre informazioni, vedere [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain) . |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
