---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 871a648eada2e710604d5fb42ea83a8559049b6a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876022"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sincronizzazione file di Azure usare il collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |La creazione di un endpoint privato per la risorsa del servizio di sincronizzazione archiviazione indicata consente di fare riferimento alla risorsa del servizio di sincronizzazione archiviazione dall'interno dello spazio di indirizzi IP privato della rete dell'organizzazione, anziché tramite l'endpoint pubblico accessibile da Internet. La creazione di un endpoint privato da solo non disabilita l'endpoint pubblico. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Configurare Sincronizzazione file di Azure con endpoint privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Viene distribuito un endpoint privato per la risorsa del servizio di sincronizzazione archiviazione indicata. In questo modo è possibile fare riferimento alla risorsa del servizio di sincronizzazione archiviazione dall'interno dello spazio di indirizzi IP privato della rete dell'organizzazione, anziché tramite l'endpoint pubblico accessibile da Internet. L'esistenza di uno o più endpoint privati da soli non disabilita l'endpoint pubblico. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modifica: configurare la Sincronizzazione file di Azure per disabilitare l'accesso alla rete pubblica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |L Sincronizzazione file di Azure'endpoint pubblico accessibile da Internet dell'utente è disabilitato dai criteri dell'organizzazione. È comunque possibile accedere al servizio di sincronizzazione archiviazione tramite i relativi endpoint privati. |Modify, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[L'accesso alla rete pubblica deve essere disabilitato per Sincronizzazione file di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |La disabilitazione dell'endpoint pubblico consente di limitare l'accesso alla risorsa del servizio di sincronizzazione archiviazione alle richieste destinate a endpoint privati approvati nella rete dell'organizzazione. Non c'è nulla di intrinsecamente non sicuro nell'consentire le richieste all'endpoint pubblico, tuttavia è possibile disabilitarlo per soddisfare i requisiti normativi, legali o dei criteri dell'organizzazione. È possibile disabilitare l'endpoint pubblico per un servizio di sincronizzazione archiviazione impostando incomingTrafficPolicy della risorsa su AllowVirtualNetworksOnly. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
