---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7ecff61cb1e72c8c24c9be1588819219a5792f6c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284846"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controlla i computer Windows in cui l'agente di Log Analytics non è connesso come previsto](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Richiede che i prerequisiti vengano distribuiti nell'ambito dell'assegnazione dei criteri. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). I computer saranno non conformi se l'agente non è installato o se è installato ma l'oggetto COM AgentConfigManager.MgmtSvcCfg restituisce che è registrato in un'area di lavoro diversa dall'ID specificato nel parametro dei criteri. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[L'agente di Log Analytics deve essere installato nei set di scalabilità di macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Questo criterio controlla i set di scalabilità di macchine virtuali Windows/Linux in cui non è installato l'agente di Log Analytics. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[L'agente di Log Analytics deve essere installato nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Questo criterio controlla le macchine virtuali Windows/Linux in cui non è installato l'agente di Log Analytics. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
