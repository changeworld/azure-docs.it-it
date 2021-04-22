---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 613c93ca7f8ad374703b2d021d6cbe8c72a7d7a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881242"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il provisioning automatico dell'agente di Log Analytics nella sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Per monitorare le minacce e le vulnerabilità della sicurezza, Centro sicurezza di Azure raccoglie i dati dalle macchine virtuali di Azure. I dati vengono raccolti dall'agente di Log Analytics, precedentemente noto come Microsoft Monitoring Agent (MMA), che esegue la lettura di varie configurazioni relative alla sicurezza e log eventi dalla macchina virtuale e copia i dati nell'area di lavoro Log Analytics per l'analisi. È consigliabile abilitare il provisioning automatico per distribuire automaticamente l'agente in tutte le macchine virtuali di Azure supportate e in tutte le nuove macchine virtuali che vengono create. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Il profilo di log di Monitoraggio di Azure deve raccogliere i log per le categorie 'scrittura', 'eliminazione' e 'azione'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Questo criterio garantisce che un profilo di log raccolga i log per le categorie 'scrittura, 'eliminazione' e 'azione' |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Monitoraggio di Azure deve raccogliere i log attività da tutte le aree](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Questo criterio controlla il profilo del log di Monitoraggio di Azure che non esporta le attività da tutte le aree supportate da Azure, incluse quelle globali. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
