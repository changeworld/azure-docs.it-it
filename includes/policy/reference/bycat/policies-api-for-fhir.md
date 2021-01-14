---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 623822dc4356cad95b247f0c475bfa43ded7ec7a
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047423"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'account API di Azure per FHIR deve usare una chiave gestita dal cliente per la crittografia dei dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Usare una chiave gestita dal cliente per controllare la crittografia dei dati inattivi archiviati in API di Azure per FHIR quando è richiesto da un requisito normativo o di conformità. Le chiavi gestite dal cliente offrono anche la doppia crittografia con l'aggiunta di un secondo livello di crittografia su quello predefinito eseguito con le chiavi gestite dal servizio. |audit, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[L'API di Azure per FHIR deve usare un collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |L'API di Azure per FHIR deve avere almeno una connessione a endpoint privato approvata. I client in una rete virtuale possono accedere in modo sicuro alle risorse con connessioni a endpoint privati tramite collegamenti privati. Per altre informazioni, vedere [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS non deve consentire a tutti i domini di accedere all'API per FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'API per FHIR. Per proteggere l'API per FHIR, rimuovere l'accesso per tutti i domini e definire in modo esplicito i domini a cui è consentita la connessione. |audit, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
