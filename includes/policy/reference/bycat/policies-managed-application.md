---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 308c94332ac71f873b32178fe6da418c9079010f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866837"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La definizione di applicazione per l'applicazione gestita deve usare l'account di archiviazione fornito dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Usare il proprio account di archiviazione per controllare i dati della definizione di applicazione quando si tratta di un requisito normativo o di conformità. È possibile scegliere di archiviare la definizione di applicazione gestita in un account di archiviazione fornito dall'utente corrente durante la creazione, per poter gestire la località e l'accesso al fine di soddisfare i requisiti di conformità normativi. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Distribuisci le associazioni per un'applicazione gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Distribuisce una risorsa di associazione che associa i tipi di risorse selezionate all'applicazione gestita specificata.  Questa distribuzione dei criteri non supporta i tipi di risorse annidati. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
