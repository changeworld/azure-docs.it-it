---
title: Creare un pool con gli indirizzi IP pubblici specificati
description: Informazioni su come creare un pool di Batch che usa i propri indirizzi IP pubblici.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: 82a37f96a91bdad37c1a7828ef0cf71b3581ca82
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768396"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Creare un pool Azure Batch con gli indirizzi IP pubblici specificati

Quando si crea un pool Azure Batch, è possibile effettuare il provisioning del [pool in](batch-virtual-network.md) una subnet di una rete virtuale di Azure specificata. Le macchine virtuali nel pool di Batch sono accessibili tramite indirizzi IP pubblici creati da Batch. Questi indirizzi IP pubblici possono cambiare nel corso della durata del pool, il che significa che le impostazioni di rete possono diventare obsolete se gli indirizzi IP non vengono aggiornati.

È possibile creare un elenco di indirizzi IP pubblici statici da usare con le macchine virtuali nel pool. In questo modo è possibile controllare l'elenco di indirizzi IP pubblici e assicurarsi che non cambino in modo imprevisto. Ciò può essere particolarmente utile se si lavora con qualsiasi servizio esterno, ad esempio un database, che limita l'accesso a determinati indirizzi IP.

Per informazioni sulla creazione di pool senza indirizzi IP pubblici, vedere Creare [un pool Azure Batch senza indirizzi IP pubblici.](./batch-pool-no-public-ip-address.md)

## <a name="prerequisites"></a>Prerequisiti

- **Autenticazione**. Per usare un indirizzo IP pubblico, l'API del client Batch deve [usare Azure Active Directory (AD) .](batch-aad-auth.md)

- **Una rete virtuale di Azure**. È necessario usare una [rete virtuale](batch-virtual-network.md) dalla stessa sottoscrizione di Azure in cui si creano il pool e gli indirizzi IP. È Azure Resource Manager solo reti virtuali basate su rete virtuale. Assicurarsi che la rete virtuale soddisfi tutti i [requisiti generali](batch-virtual-network.md#vnet-requirements).

- **Almeno un indirizzo IP pubblico di Azure.** Per creare uno o più indirizzi IP pubblici, è possibile usare il [portale di Azure](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), l'interfaccia [della Command-Line](/cli/azure/network/public-ip#az_network_public_ip_create)di Azure o [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Assicurarsi di seguire i requisiti elencati di seguito.

> [!NOTE]
> Batch alloca automaticamente risorse di rete aggiuntive nel gruppo di risorse contenente gli indirizzi IP pubblici. Per ogni 100 nodi dedicati, Batch alloca in genere un gruppo di sicurezza di rete (NSG) e un servizio di bilanciamento del carico. Queste risorse sono limitate dalle quote di risorse della sottoscrizione. Quando si usano pool di dimensioni maggiori, potrebbe essere necessario [richiedere un aumento della quota](batch-quota-limit.md#increase-a-quota) per una o più di queste risorse.

## <a name="public-ip-address-requirements"></a>Requisiti degli indirizzi IP pubblici

Quando si creano gli indirizzi IP pubblici, tenere presenti i requisiti seguenti:

- Gli indirizzi IP pubblici devono essere nella stessa sottoscrizione e nella stessa area dell'account Batch che si usa per creare il pool.
- **L'assegnazione dell'indirizzo IP** deve essere impostata su **Statico.**
- **Lo SKU** deve essere impostato su **Standard.**
- È necessario specificare un nome DNS.
- Gli indirizzi IP pubblici devono essere usati solo per i pool di configurazione delle macchine virtuali. Nessun'altra risorsa deve usare questi indirizzi IP oppure nel pool potrebbero verificarsi errori di allocazione.
- Nessun criterio di sicurezza o blocco delle risorse deve limitare l'accesso dell'utente all'indirizzo IP pubblico.
- Il numero di indirizzi IP pubblici specificato per il pool deve essere sufficientemente grande da contenere il numero di macchine virtuali di destinazione per il pool. Deve corrispondere almeno alla somma delle proprietà **targetDedicatedNodes** e    **targetLowPriorityNodes**   del pool. Se gli indirizzi IP non sono sufficienti, il pool alloca parzialmente i nodi di calcolo e si verifica un errore di ridimensionamento. Attualmente, Batch usa un indirizzo IP pubblico per ogni 100 macchine virtuali.
- Avere sempre un buffer aggiuntivo di indirizzi IP pubblici. È consigliabile aggiungere almeno un indirizzo IP pubblico aggiuntivo o circa il 10% degli indirizzi IP pubblici totali aggiunti a un pool, a seconda del valore maggiore. Questo buffer aggiuntivo consente a Batch di eseguire l'ottimizzazione interna durante la riduzione, oltre a consentire un aumento più rapido delle prestazioni dopo un aumento o una riduzione non riusciti.
- Dopo aver creato il pool, non è possibile aggiungere o modificare l'elenco di indirizzi IP pubblici usati dal pool. Se è necessario modificare l'elenco, è necessario eliminare il pool e quindi ricrearlo.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Creare un pool di Batch con indirizzi IP pubblici

L'esempio seguente illustra come usare il [Azure Batch API REST del servizio](/rest/api/batchservice/pool/add) per creare un pool che usa indirizzi IP pubblici.

### <a name="batch-service-rest-api"></a>API REST per il servizio Batch

URI DELL'API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Request Body

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md) come pool, nodi, processi e attività.
- Informazioni sulla [creazione di un pool in una subnet di una rete virtuale di Azure.](batch-virtual-network.md)
- Informazioni sulla [creazione di un pool Azure Batch senza indirizzi IP pubblici.](./batch-pool-no-public-ip-address.md)
