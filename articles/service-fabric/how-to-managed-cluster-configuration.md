---
title: Configurare il cluster gestito Service Fabric (anteprima)
description: Informazioni su come configurare il cluster gestito Service Fabric per gli aggiornamenti automatici del sistema operativo, le regole NSG e altro ancora.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732633"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Opzioni di configurazione di Service Fabric cluster gestito (anteprima)

Oltre a selezionare lo [SKU del cluster gestito Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) durante la creazione del cluster, sono disponibili diversi altri modi per configurarlo. Nell'anteprima corrente è possibile:

* Configurare le [Opzioni di rete](how-to-managed-cluster-networking.md) per il cluster
* Aggiungere un' [estensione del set di scalabilità di macchine virtuali](how-to-managed-cluster-vmss-extension.md) a un tipo di nodo
* Configurare l' [identità gestita](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) nei tipi di nodo
* Abilitare gli [aggiornamenti automatici del sistema operativo](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) per i nodi
* Abilitare la [crittografia del disco dati e del sistema operativo](how-to-enable-managed-cluster-disk-encryption.md) nei nodi

## <a name="enable-automatic-os-image-upgrades"></a>Abilitare gli aggiornamenti automatici delle immagini del sistema operativo

È possibile scegliere di abilitare gli aggiornamenti automatici delle immagini del sistema operativo per le macchine virtuali che eseguono i nodi del cluster gestito. Sebbene le risorse del set di scalabilità di macchine virtuali siano gestite per conto dell'utente con Service Fabric cluster gestiti, è possibile scegliere di abilitare gli aggiornamenti automatici delle immagini del sistema operativo per i nodi del cluster. Come per i cluster di [Service Fabric classici](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) , i nodi del cluster gestiti non vengono aggiornati per impostazione predefinita, in modo da impedire indesiderate rotture del cluster.

Per abilitare gli aggiornamenti automatici del sistema operativo:

* Usare la `2021-01-01-preview` versione (o versioni successive) delle risorse *Microsoft. ServiceFabric/Managedclusters* e *Microsoft. ServiceFabric/managedclusters/NodeTypes*
* Imposta la proprietà del cluster `enableAutoOSUpgrade` su *true*
* Imposta la proprietà della risorsa nodeTypes del cluster `vmImageVersion` su *più recente*

Ad esempio:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Una volta abilitata, Service Fabric inizierà a eseguire query e a tenere traccia delle versioni delle immagini del sistema operativo nel cluster gestito. Se è disponibile una nuova versione del sistema operativo, i tipi di nodo del cluster (set di scalabilità di macchine virtuali) verranno aggiornati uno alla volta. Gli aggiornamenti del Runtime Service Fabric vengono eseguiti solo dopo la conferma che non sono in corso aggiornamenti dell'immagine del sistema operativo del nodo del cluster.

Se un aggiornamento ha esito negativo, Service Fabric tenterà di riprovare dopo 24 ore, per un massimo di tre tentativi. Analogamente agli aggiornamenti di Service Fabric classici (non gestiti), le app o i nodi non integri possono bloccare l'aggiornamento dell'immagine del sistema operativo.

Per altre informazioni sugli aggiornamenti delle immagini, vedere [aggiornamenti automatici delle immagini del sistema operativo con set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Service Fabric cluster gestiti](overview-managed-cluster.md)

[Modelli di cluster di Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)
