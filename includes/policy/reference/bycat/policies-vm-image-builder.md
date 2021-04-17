---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2cec02d309e18b26c4078ad888ad736da5c9e24
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499245"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I modelli di Image Builder per macchine virtuali devono usare un collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma Collegamento privato gestisce la connettività tra il consumer e i servizi attraverso la rete backbone di Azure. Il mapping di endpoint privati alla macchina virtuale Image Builder risorse di compilazione riduce i rischi di perdita dei dati. Per altre informazioni sui collegamenti privati, vedere: [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet) . |Audit, Disabled, Deny |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
