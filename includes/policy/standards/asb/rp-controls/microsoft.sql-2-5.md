---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c61f7bbe9f926ad186710cc6e804060d1e30950
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511993"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I server SQL con controllo sulla destinazione dell'account di archiviazione devono essere configurati con conservazione di 90 giorni o superiore](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Ai fini dell'analisi degli eventi imprevisti, è consigliabile impostare la conservazione dei dati per il controllo SQL Server'account di archiviazione sulla destinazione dell'account di archiviazione su almeno 90 giorni. Verificare di soddisfare le regole di conservazione necessarie per le aree in cui si opera. Questa operazione è talvolta necessaria per la conformità agli standard normativi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
