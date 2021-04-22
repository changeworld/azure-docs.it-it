---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e6cdbadd01e93a172389bf67474d0736bc2a7687
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881323"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il provisioning automatico dell'agente di Log Analytics nella sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Per monitorare le minacce e le vulnerabilità della sicurezza, Centro sicurezza di Azure raccoglie i dati dalle macchine virtuali di Azure. I dati vengono raccolti dall'agente di Log Analytics, precedentemente noto come Microsoft Monitoring Agent (MMA), che esegue la lettura di varie configurazioni relative alla sicurezza e log eventi dalla macchina virtuale e copia i dati nell'area di lavoro Log Analytics per l'analisi. È consigliabile abilitare il provisioning automatico per distribuire automaticamente l'agente in tutte le macchine virtuali di Azure supportate e in tutte le nuove macchine virtuali che vengono create. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
