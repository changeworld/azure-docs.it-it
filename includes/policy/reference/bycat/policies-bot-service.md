---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f26c7a4e9a571c317aa9cdfa1267778b607430be
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100238"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'endpoint del servizio Bot deve essere un URI HTTPS valido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |I dati possono essere alterati durante la trasmissione. Esistono protocolli contenenti la crittografia per risolvere i problemi di uso improprio e manomissioni. Per assicurarsi che i bot comunichino solo su canali crittografati, impostare l'endpoint su un URI HTTPS valido. Ciò garantisce che venga usato il protocollo HTTPS per crittografare i dati in transito ed è spesso un requisito per la conformità agli standard di settore o alle normative. Visitare: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Il servizio bot deve essere crittografato con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Il servizio Azure bot crittografa automaticamente la risorsa per proteggere i dati e soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, vengono utilizzate le chiavi di crittografia gestite da Microsoft. Per una maggiore flessibilità nella gestione delle chiavi o per il controllo dell'accesso alla sottoscrizione, selezionare chiavi gestite dal cliente, note anche come BYOK (Bring your own key). Altre informazioni sulla crittografia del servizio Azure bot: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
