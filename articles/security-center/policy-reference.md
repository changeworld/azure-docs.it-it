---
title: Definizioni di criteri predefiniti per Centro sicurezza di Azure
description: Elenco di definizioni di criteri predefiniti di Criteri di Azure per Centro sicurezza di Azure. Queste definizioni di criteri predefiniti forniscono approcci comuni alla gestione delle risorse di Azure.
ms.date: 04/14/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 8023dcc29fe8ea3af853237e911524549e4d2bcb
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739639"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definizioni predefinite di Criteri di Azure per Centro sicurezza di Azure

Questa pagina è un indice [Criteri di Azure](../governance/policy/overview.md) definizioni di criteri predefiniti correlate Centro sicurezza di Azure. Sono disponibili i raggruppamenti seguenti di definizioni di criteri:

- Il [gruppo di iniziative](#azure-security-center-initiatives) elenca le Criteri di Azure di iniziative nella categoria "Centro sicurezza".
- Il [gruppo di iniziative](#azure-security-center-initiatives) predefinito elenca tutte le Criteri di Azure che fanno parte dell'iniziativa predefinita del Centro sicurezza, Azure Security [Benchmark.](https://docs.microsoft.com/security/benchmark/azure/introduction) Questo benchmark, ampiamente rispettato e creato da Microsoft, si basa sui controlli del [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e del National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.
- Il [gruppo](#azure-security-center-category) di categorie elenca tutte le Criteri di Azure nella categoria "Centro sicurezza".

Per altre informazioni sui criteri di sicurezza, vedere [Utilizzo dei criteri di sicurezza](./tutorial-security-policy.md). Per informazioni su altre definizioni predefinite di Criteri di Azure per altri servizi, vedere [Definizioni di criteri predefiniti di Criteri di Azure](../governance/policy/samples/built-in-policies.md).

Il nome di ogni definizione di criterio predefinito punta alla definizione del criterio nel portale di Azure. Usare il collegamento nella colonna **Versione** per visualizzare l'origine nel [repository GitHub di Criteri di Azure](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Centro sicurezza di Azure iniziative

Per informazioni sulle iniziative incorporate monitorate dal Centro sicurezza, vedere la tabella seguente:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Iniziativa predefinita del Centro sicurezza (Azure Security Benchmark)

Per informazioni sui criteri predefiniti monitorati dal Centro sicurezza, vedere la tabella seguente:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Centro sicurezza di Azure categorie

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi i Criteri di Azure dei criteri di sicurezza nel Centro sicurezza. Per altre informazioni su iniziative, criteri e modalità di relazione con le raccomandazioni del Centro sicurezza, vedere Che cosa sono i criteri di sicurezza, le iniziative [e le raccomandazioni?](security-policy-concept.md).
