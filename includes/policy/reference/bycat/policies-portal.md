---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 57b257711118aa21550116251cb983923b405ce3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611244"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I dashboard condivisi non devono includere riquadri markdown con contenuto inline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Non consente la creazione di un dashboard condiviso con contenuto inline in riquadri di markdown e impone che il contenuto venga archiviato come file markdown ospitato online. Se si usa contenuto inline nel riquadro markdown, non sarà possibile gestire la crittografia del contenuto. Configurando una risorsa di archiviazione personalizzata, è possibile applicare la crittografia, la crittografia doppia e usare chiavi personalizzate (BYOK). L'abilitazione di questo criterio impone agli utenti di usare la versione 2020-09-01-preview o successiva delle API REST per i dashboard condivisi. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
