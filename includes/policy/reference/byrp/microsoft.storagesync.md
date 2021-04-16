---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0478e606cd5eb6652b992b647d5f69b347753341
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504272"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sincronizzazione file di Azure usare il collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |La creazione di un endpoint privato per la risorsa del servizio di sincronizzazione archiviazione indicata consente di indirizzarsi alla risorsa del servizio di sincronizzazione archiviazione dall'interno dello spazio di indirizzi IP privato della rete dell'organizzazione, anziché tramite l'endpoint pubblico accessibile tramite Internet. La creazione di un endpoint privato non disabilita l'endpoint pubblico. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Configurare Sincronizzazione file di Azure con endpoint privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Viene distribuito un endpoint privato per la risorsa del servizio di sincronizzazione archiviazione indicata. Ciò consente di risolvere la risorsa del servizio di sincronizzazione archiviazione dall'interno dello spazio indirizzi IP privato della rete dell'organizzazione, anziché tramite l'endpoint pubblico accessibile tramite Internet. L'esistenza di uno o più endpoint privati non disabilita l'endpoint pubblico. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modifica- Configurare Sincronizzazione file di Azure per disabilitare l'accesso alla rete pubblica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |L Sincronizzazione file di Azure dell'endpoint pubblico accessibile a Internet dell'utente è disabilitato dai criteri dell'organizzazione. È comunque possibile accedere al servizio di sincronizzazione archiviazione tramite i relativi endpoint privati. |Modifica, Disabilitata |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[L'accesso alla rete pubblica deve essere disabilitato per Sincronizzazione file di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |La disabilitazione dell'endpoint pubblico consente di limitare l'accesso alla risorsa del servizio di sincronizzazione archiviazione alle richieste destinate a endpoint privati approvati nella rete dell'organizzazione. Non c'è nulla di intrinsecamente insicuro nell'consentire le richieste all'endpoint pubblico, tuttavia è possibile disabilitarlo per soddisfare i requisiti normativi, legali o dei criteri aziendali. È possibile disabilitare l'endpoint pubblico per un servizio di sincronizzazione archiviazione impostando incomingTrafficPolicy della risorsa su AllowVirtualNetworksOnly. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
