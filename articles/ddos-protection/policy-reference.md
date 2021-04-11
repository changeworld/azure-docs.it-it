---
title: Definizioni predefinite dei criteri per la protezione DDoS di Azure standard
description: Elenca le definizioni dei criteri predefiniti di criteri di Azure per la protezione DDoS di Azure standard. Queste definizioni di criteri predefinite forniscono approcci comuni alla gestione delle risorse di Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 6a0496740fbd82090ba7199ca3e064f5bd2bf7c5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108268"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Definizioni predefinite di criteri di Azure per la protezione DDoS di Azure standard

Questa pagina è un indice delle definizioni dei criteri predefiniti di [criteri di Azure](../governance/policy/overview.md) per la protezione DDoS di Azure standard. Per informazioni su altre definizioni predefinite di Criteri di Azure per altri servizi, vedere [Definizioni di criteri predefiniti di Criteri di Azure](../governance/policy/samples/built-in-policies.md).

Il nome di ogni definizione di criterio predefinito punta alla definizione del criterio nel portale di Azure. Usare il collegamento nella colonna **Versione** per visualizzare l'origine nel [repository GitHub di Criteri di Azure](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Protezione DDoS di Azure Standard

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le reti virtuali devono essere protette da protezione DDoS di Azure standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Proteggi le tue reti virtuali da attacchi volumetrici e di protocollo con protezione DDoS di Azure standard. Per altre informazioni, vedere [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs).|Modifica, controllo, disabilitazione|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Per gli indirizzi IP pubblici devono essere abilitati i log delle risorse per la protezione DDoS di Azure standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Abilitare i log delle risorse per gli indirizzi IP pubblici nelle impostazioni di diagnostica per eseguire lo streaming in un'area di lavoro Log Analytics. Ottenere visibilità dettagliata sul traffico degli attacchi e le azioni intraprese per attenuare gli attacchi DDoS tramite notifiche, report e log dei flussi.|AuditIfNotExists, DeployIfNotExists, disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Passaggi successivi

- Vedere i criteri predefiniti nel [repository di GitHub su Criteri di Azure](https://github.com/Azure/azure-policy).
- Vedere la [struttura delle definizioni di Criteri di Azure](../governance/policy/concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).