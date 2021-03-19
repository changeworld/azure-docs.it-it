---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d13824e895d553795e28c7603082305e380f2f99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605577"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le app per la logica ambiente del servizio di integrazione devono essere crittografate con chiavi gestite dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Distribuire in ambiente del servizio di integrazione per gestire la crittografia dei dati inattivi delle app per la logica usando chiavi gestite dal cliente. Per impostazione predefinita, i dati dei clienti vengono crittografati con chiavi gestite dal servizio, ma le chiavi gestite dal cliente sono in genere necessarie per soddisfare gli standard di conformità normativi. Le chiavi gestite dal cliente consentono la crittografia dei dati con una chiave di Azure Key Vault creata e di proprietà dell'utente. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[Le app per la logica devono essere distribuite in ambiente del servizio di integrazione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |La distribuzione di app per la logica in ambiente del servizio di integrazione in una rete virtuale Sblocca le funzionalità di rete e di sicurezza avanzate per le app per la logica e offre un maggiore controllo sulla configurazione di rete. Per altre informazioni, vedere [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment). La distribuzione in ambiente del servizio di integrazione consente inoltre la crittografia con chiavi gestite dal cliente che offre una protezione avanzata dei dati consentendo di gestire le chiavi di crittografia. Spesso è necessario soddisfare i requisiti di conformità. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[I log delle risorse nelle app per la logica devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Controllare l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
