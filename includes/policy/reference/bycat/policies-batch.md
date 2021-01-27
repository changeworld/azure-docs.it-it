---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 99821231f61c83639ba37f6e9da2a4d73e5a1e69
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807621"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'account Azure Batch deve usare chiavi gestite dal cliente per la crittografia dei dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |Usare le chiavi gestite dal cliente per gestire la crittografia dei dati inattivi dell'account Batch. Per impostazione predefinita, i dati dei clienti sono crittografati tramite chiavi gestite dal servizio, ma in genere sono richieste chiavi gestite dal cliente per soddisfare gli standard di conformità alle normative. Le chiavi gestite dal cliente consentono di crittografare i dati con una chiave di Azure Key Vault creata dall'utente e di sua proprietà. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Batch/Batch_CustomerManagedKey_Audit.json) |
|[È consigliabile abilitare i log di diagnostica negli account Batch](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[È consigliabile configurare le regole di avviso per le metriche negli account Batch](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Controlla la configurazione delle regole di avviso delle metriche per l'account Batch per abilitare la metrica richiesta |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
