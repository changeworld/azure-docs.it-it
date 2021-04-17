---
title: Creare un pool con la crittografia disco abilitata
description: Informazioni su come usare la configurazione della crittografia del disco per crittografare i nodi con una chiave gestita dalla piattaforma.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 40281211e5eb70089f4168dcb02720c912120a35
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576109"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Creare un pool con la crittografia disco abilitata

Quando si crea un pool Azure Batch usando Configurazione macchina virtuale [,](nodes-and-pools.md#virtual-machine-configuration)è possibile crittografare i nodi di calcolo nel pool con una chiave gestita dalla piattaforma specificando la configurazione di crittografia del disco.

Questo articolo illustra come creare un pool di Batch con la crittografia del disco abilitata.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Perché usare un pool con la configurazione della crittografia del disco?

Con un pool di Batch è possibile accedere ai dati nel sistema operativo e nei dischi temporanei del nodo di calcolo e archiviarli. La crittografia del disco lato server con una chiave gestita dalla piattaforma consente di proteggere questi dati con un sovraccarico ridotto e praticità.

Batch applierà una di queste tecnologie di crittografia dei dischi nei nodi di calcolo, in base alla configurazione del pool e al supporto a livello di a livello di regione.

- [Crittografia dei dischi gestiti in stato di inquieto con chiavi gestite dalla piattaforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Crittografia nell'host con una chiave gestita dalla piattaforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Non sarà possibile specificare quale metodo di crittografia verrà applicato ai nodi nel pool. Si forniscono invece i dischi di destinazione da crittografare nei relativi nodi e Batch può scegliere il metodo di crittografia appropriato, assicurandosi che i dischi specificati siano crittografati nel nodo di calcolo.

> [!IMPORTANT]
> Se si crea il pool con [un'immagine personalizzata,](batch-sig-images.md)è possibile abilitare la crittografia del disco solo se si usano macchine virtuali Windows.

## <a name="azure-portal"></a>Portale di Azure

Quando si crea un pool di Batch nel portale di Azure, selezionare **TemporaryDisk** o **OsAndTemporaryDisk** in **Disk Encryption Configuration (Configurazione crittografia disco).**

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Screenshot dell'opzione Configurazione crittografia dischi nella portale di Azure.":::

Dopo aver creato il pool, è possibile visualizzare le destinazioni di configurazione della crittografia dei dischi nella **sezione Proprietà del** pool.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Screenshot che mostra le destinazioni di configurazione della crittografia del disco nel portale di Azure.":::

## <a name="examples"></a>Esempio

Gli esempi seguenti illustrano come crittografare i dischi temporanei e del sistema operativo in un pool di Batch usando Batch .NET SDK, l'API REST di Batch e l'interfaccia della riga di comando di Azure.

### <a name="batch-net-sdk"></a>SDK di .NET per Batch

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>API Batch REST

URL API REST:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo della richiesta:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [crittografia lato server di archiviazione su disco di Azure](../virtual-machines/disk-encryption.md).
- Per una panoramica approfondita di Batch, vedere [Flusso di lavoro e risorse del servizio Batch](batch-service-workflow-features.md).
