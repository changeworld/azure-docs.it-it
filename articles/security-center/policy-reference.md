---
title: Definizioni di criteri predefiniti per Centro sicurezza di Azure
description: Elenco di definizioni di criteri predefiniti di Criteri di Azure per Centro sicurezza di Azure. Queste definizioni di criteri predefiniti forniscono approcci comuni alla gestione delle risorse di Azure.
ms.date: 03/24/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: a96dcc3b6f4abafd6cf72c11b2f52480fe3780ec
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105036705"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definizioni predefinite di Criteri di Azure per Centro sicurezza di Azure

Questa pagina è un indice delle definizioni dei criteri predefiniti di [criteri di Azure](../governance/policy/overview.md) correlate al centro sicurezza di Azure. Sono disponibili i seguenti raggruppamenti di definizioni dei criteri:

- Il gruppo [iniziative](#azure-security-center-initiatives) elenca le definizioni delle iniziative di criteri di Azure nella categoria "Centro sicurezza".
- Il gruppo [Initiative predefinito](#azure-security-center-initiatives) elenca tutte le definizioni di criteri di Azure che fanno parte dell'iniziativa predefinita del Centro sicurezza, [benchmark di sicurezza di Azure](../security/benchmarks/introduction.md). Questo benchmark creato da Microsoft, ampiamente rispettato si basa sui controlli provenienti da [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.
- Il gruppo [categoria](#azure-security-center-category) elenca tutte le definizioni di criteri di Azure nella categoria "Centro sicurezza".

Per altre informazioni sui criteri di sicurezza, vedere [Utilizzo dei criteri di sicurezza](./tutorial-security-policy.md). Per informazioni su altre definizioni predefinite di Criteri di Azure per altri servizi, vedere [Definizioni di criteri predefiniti di Criteri di Azure](../governance/policy/samples/built-in-policies.md).

Il nome di ogni definizione di criterio predefinito punta alla definizione del criterio nel portale di Azure. Usare il collegamento nella colonna **Versione** per visualizzare l'origine nel [repository GitHub di Criteri di Azure](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Iniziative del Centro sicurezza di Azure

Per informazioni sulle iniziative predefinite monitorate dal centro sicurezza, vedere la tabella seguente:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Iniziativa predefinita del Centro sicurezza (benchmark di sicurezza di Azure)

Per informazioni sui criteri predefiniti monitorati dal centro sicurezza, vedere la tabella seguente:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Categoria Centro sicurezza di Azure

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato le definizioni dei criteri di sicurezza di criteri di Azure nel centro sicurezza. Per altre informazioni sulle iniziative, i criteri e su come sono correlati alle raccomandazioni del Centro sicurezza, vedere informazioni su [criteri di sicurezza, iniziative e consigli](security-policy-concept.md).
