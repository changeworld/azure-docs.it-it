---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60ddf7a90f478fa0b286f14a1cba4d609b02e321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020867"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server deve conservare i dati di controllo per almeno 90 giorni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Per finalità di analisi degli eventi imprevisti, è consigliabile impostare la conservazione dei dati per i dati di controllo di SQL Server su almeno 90 giorni. Verificare che le regole di conservazione necessarie per le aree in cui si opera siano rispettate. Questa operazione è talvolta necessaria per la conformità agli standard normativi. |AuditIfNotExists, Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
