---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e884d11aa3d86a2d9e156aeb14cf4d53a301f2b2
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738731"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'endpoint del servizio Bot deve essere un URI HTTPS valido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |I dati possono essere alterati durante la trasmissione. Esistono protocolli contenenti la crittografia per risolvere i problemi di uso improprio e manomissioni. Per assicurarsi che i bot comunichino solo su canali crittografati, impostare l'endpoint su un URI HTTPS valido. Ciò garantisce che venga usato il protocollo HTTPS per crittografare i dati in transito ed è spesso un requisito per la conformità agli standard di settore o alle normative. Visitare: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
