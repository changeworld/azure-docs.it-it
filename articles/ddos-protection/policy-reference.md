---
title: Definizioni di criteri predefiniti per Protezione DDoS di Azure Standard
description: Elenca Criteri di Azure definizioni di criteri predefiniti per Protezione DDoS di Azure Standard. Queste definizioni di criteri predefinite forniscono approcci comuni alla gestione delle risorse di Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: b58dddef0aa63aec525e039f333a7a785c32d23e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875648"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Criteri di Azure definizioni incorporate per Protezione DDoS di Azure Standard

Questa pagina è un indice [di](../governance/policy/overview.md) Criteri di Azure di criteri predefiniti per Protezione DDoS di Azure Standard. Per informazioni su altre definizioni predefinite di Criteri di Azure per altri servizi, vedere [Definizioni di criteri predefiniti di Criteri di Azure](../governance/policy/samples/built-in-policies.md).

Il nome di ogni definizione di criterio predefinito punta alla definizione del criterio nel portale di Azure. Usare il collegamento nella colonna **Versione** per visualizzare l'origine nel [repository GitHub di Criteri di Azure](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Protezione DDoS di Azure Standard

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le reti virtuali devono essere protette Protezione DDoS di Azure Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Proteggere le reti virtuali da attacchi volumetrici e di protocollo con Protezione DDoS di Azure Standard. Per altre informazioni, vedere [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs).|Modifica, Controllo, Disabilitato|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Gli indirizzi IP pubblici devono avere i log delle risorse abilitati per Protezione DDoS di Azure Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Abilitare i log delle risorse per gli indirizzi IP pubblici nelle impostazioni di diagnostica per lo streaming in un'area di lavoro Log Analytics. Ottenere una visibilità dettagliata sul traffico di attacco e sulle azioni intraprese per mitigare gli attacchi DDoS tramite notifiche, report e log di flusso.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Passaggi successivi

- Vedere i criteri predefiniti nel [repository di GitHub su Criteri di Azure](https://github.com/Azure/azure-policy).
- Vedere la [struttura delle definizioni di Criteri di Azure](../governance/policy/concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).
