---
title: Creare un pool tra le zone di disponibilità
description: Informazioni su come creare un pool di batch con criteri di zona per la protezione da errori.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 98109e1b74106bc636eaa715575e4b30ab29f9e2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056111"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Creare un pool di Azure Batch tra zone di disponibilità

Le aree di Azure che supportano [zone di disponibilità](https://azure.microsoft.com/global-infrastructure/availability-zones/) hanno almeno tre zone separate, ciascuna con il proprio sistema di alimentazione, rete e raffreddamento indipendente. Quando si crea un pool di Azure Batch usando la configurazione della macchina virtuale, è possibile scegliere di effettuare il provisioning del pool di batch tra zone di disponibilità. La creazione del pool con questi criteri di zona consente di proteggere i nodi di calcolo batch da errori a livello di Data Center di Azure.

Ad esempio, è possibile creare il pool con criteri di zona in un'area di Azure che supporta tre zone di disponibilità. Se un Data Center di Azure in una zona di disponibilità presenta un errore di infrastruttura, il pool di batch avrà ancora nodi integri negli altri due zone di disponibilità, quindi il pool rimarrà disponibile per la pianificazione delle attività.

## <a name="regional-support-and-other-requirements"></a>Supporto regionale e altri requisiti

Batch mantiene la parità con Azure in zone di disponibilità di supporto. Per usare l'opzione di zona, è necessario creare il pool in un' [area di Azure supportata](../availability-zones/az-region.md).

Per poter allocare il pool di batch tra le zone di disponibilità, l'area di Azure in cui viene creato il pool deve supportare lo SKU di VM richiesto in più di una zona. È possibile convalidare questo problema chiamando l' [API elenco SKU risorse](/rest/api/compute/resourceskus/list) e controllare il campo **LocationInfo** di [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Assicurarsi che più di una zona sia supportata per lo SKU della macchina virtuale richiesto.

Per gli [account batch in modalità sottoscrizione utente](accounts.md#batch-accounts), assicurarsi che la sottoscrizione in cui si sta creando il pool non abbia una restrizione dell'offerta di zona nello SKU della macchina virtuale richiesto. Per confermare questa operazione, chiamare l' [API elenco SKU risorse](/rest/api/compute/resourceskus/list) e controllare [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Se esiste una restrizione di zona, è possibile inviare un [ticket di supporto](../azure-portal/supportability/sku-series-unavailable.md) per rimuovere la restrizione della zona.

Si noti anche che non è possibile creare un pool con criteri di zona se è abilitata la comunicazione tra nodi e usa uno [SKU di VM che supporta InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Creare un pool di batch tra zone di disponibilità

Gli esempi seguenti illustrano come creare un pool di batch tra zone di disponibilità.

> [!NOTE]
> Quando si crea il pool con un criterio di zona, il servizio batch tenterà di allocare il pool in tutti zone di disponibilità nell'area selezionata; non è possibile specificare una particolare allocazione tra le zone.

### <a name="batch-management-client-net-sdk"></a>.NET SDK client di gestione batch

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>API Batch REST

URL DELL'API REST

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Testo della richiesta

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md) come pool, nodi, processi e attività.
- Informazioni sulla [creazione di un pool in una subnet di una rete virtuale di Azure](batch-virtual-network.md).
- Informazioni sulla [creazione di un pool di Azure batch senza indirizzi IP pubblici](./batch-pool-no-public-ip-address.md).

