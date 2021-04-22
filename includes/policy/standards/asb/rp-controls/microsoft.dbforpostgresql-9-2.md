---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 25011470c85338fcc20565130c2d6765d90e74ad
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880919"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Il database di Azure per PostgreSQL consente di scegliere l'opzione di ridondanza per il server di database. Può essere impostato sull'archiviazione di backup con ridondanza geografica in cui i dati non solo vengono archiviati all'interno dell'area in cui è ospitato il server, ma vengono anche replicati in un'area associata per fornire un'opzione di ripristino in caso di errore di un'area. La configurazione dell'archiviazione con ridondanza geografica per il backup è consentita solo durante la creazione del server. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
