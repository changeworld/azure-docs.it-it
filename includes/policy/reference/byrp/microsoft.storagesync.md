---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b830d3d9143bb5b15292d1c0b14b04d8fee9b2ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588322"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sincronizzazione file di Azure deve usare il collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |La creazione di un endpoint privato per la risorsa del servizio di sincronizzazione archiviazione indicata consente di indirizzare la risorsa del servizio di sincronizzazione archiviazione dallo spazio di indirizzi IP privato della rete dell'organizzazione, anziché tramite l'endpoint pubblico accessibile da Internet. La creazione di un endpoint privato autonomamente non disabilita l'endpoint pubblico. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Configurare Sincronizzazione file di Azure con endpoint privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Viene distribuito un endpoint privato per la risorsa del servizio di sincronizzazione archiviazione indicata. In questo modo è possibile indirizzare la risorsa del servizio di sincronizzazione archiviazione dallo spazio di indirizzi IP privato della rete dell'organizzazione, anziché tramite l'endpoint pubblico accessibile da Internet. L'esistenza di uno o più endpoint privati non disabilita l'endpoint pubblico. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modificare: configurare Sincronizzazione file di Azure per disabilitare l'accesso alla rete pubblica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |L'endpoint pubblico accessibile da Internet Sincronizzazione file di Azure è disabilitato dai criteri dell'organizzazione. È comunque possibile accedere al servizio di sincronizzazione archiviazione tramite gli endpoint privati. |Modifica, disabilitazione |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[L'accesso alla rete pubblica deve essere disabilitato per Sincronizzazione file di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |La disabilitazione dell'endpoint pubblico consente di limitare l'accesso alla risorsa del servizio di sincronizzazione archiviazione alle richieste destinate ad endpoint privati approvati nella rete dell'organizzazione. Non c'è nulla di più sicuro per consentire le richieste all'endpoint pubblico, tuttavia, può essere necessario disabilitarlo per soddisfare i requisiti normativi, legali o aziendali. È possibile disabilitare l'endpoint pubblico per un servizio di sincronizzazione archiviazione impostando il incomingTrafficPolicy della risorsa su AllowVirtualNetworksOnly. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
