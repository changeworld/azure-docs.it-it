---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6b622d1c22dd9481881d2d68eb26ef34a1e5ca5c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045744"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Batch account deve usare chiavi gestite dal cliente per crittografare i dati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |Usare le chiavi gestite dal cliente (CMK) per gestire la crittografia dei dati dell'account batch. Per impostazione predefinita, i dati dei clienti vengono crittografati con chiavi gestite dal servizio, ma CMK sono in genere necessarie per soddisfare gli standard di conformità normativi. CMK consente di crittografare i dati con una chiave di Azure Key Vault creata e di proprietà dell'utente. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. Per altre informazioni sulla crittografia CMK [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK) , vedere. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Batch/Batch_CustomerManagedKey_Audit.json) |
|[Distribuisci le impostazioni di diagnostica per l'account Batch nell'hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdb51110f-0865-4a6e-b274-e2e07a5b2cd7) |Distribuisce le impostazioni di diagnostica per l'account Batch per lo streaming in un hub eventi a livello di area quando viene creato o aggiornato un account Batch in cui manca questa impostazione di diagnostica. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Distribuisci le impostazioni di diagnostica per l'account Batch nell'area di lavoro Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc84e5349-db6d-4769-805e-e14037dab9b5) |Distribuisce le impostazioni di diagnostica per l'account Batch per lo streaming in un'area di lavoro Log Analytics a livello di area quando viene creato o aggiornato un account Batch in cui manca questa impostazione di diagnostica. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[È consigliabile abilitare i log di diagnostica negli account Batch](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[È consigliabile configurare le regole di avviso per le metriche negli account Batch](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Controlla la configurazione delle regole di avviso delle metriche per l'account Batch per abilitare la metrica richiesta |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
