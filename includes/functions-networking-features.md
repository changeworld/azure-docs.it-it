---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936762"
---


| Funzionalità |[Piano a consumo](../articles/azure-functions/consumption-plan.md)|[Piano Premium](../articles/azure-functions/functions-premium-plan.md)|[Piano dedicato](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrizioni IP in ingresso e accesso al sito privato](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Sì|✅Sì|✅Sì|✅Sì|✅Sì|
|[Integrazione della rete virtuale](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Sì (Regionale)|✅Sì (Regional e Gateway)|✅Sì| ✅Sì|
|[Trigger della rete virtuale (non HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Sì |✅Sì|✅Sì|✅Sì|
|[Connessioni ibride](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (solo Windows)|❌No|✅Sì|✅Sì|✅Sì|✅Sì|
|[Restrizioni IP in uscita](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Sì|✅Sì|✅Sì|✅Sì|