---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 18cbe3f50ee107d710b21e95f6f4774ecd65263c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512107"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Il database di Azure per PostgreSQL consente di scegliere l'opzione di ridondanza per il server di database. Può essere impostato sull'archiviazione di backup con ridondanza geografica in cui i dati non solo vengono archiviati all'interno dell'area in cui è ospitato il server, ma vengono anche replicati in un'area associata per fornire un'opzione di ripristino in caso di errore di un'area. La configurazione dell'archiviazione con ridondanza geografica per il backup è consentita solo durante la creazione del server. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
