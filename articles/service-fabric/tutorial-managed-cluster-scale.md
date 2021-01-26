---
title: Aumentare il numero di istanze di un cluster gestito di Service Fabric (anteprima)
description: Questa esercitazione illustra come aumentare il numero di istanze di un tipo di nodo di un cluster gestito di Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 769bcb339b2cc1419c7a3d92d0f08130029a9f95
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785432"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Esercitazione: Aumentare il numero di istanze di un cluster gestito di Service Fabric (anteprima)

In questa serie di esercitazioni verrà illustrato quanto segue:

> [!div class="checklist"]
> * [Come distribuire un cluster gestito di Service Fabric.](tutorial-managed-cluster-deploy.md)
> * Come aumentare il numero di istanze di un cluster gestito di Service Fabric
> * [Come aggiungere e rimuovere tipi di nodo in un cluster gestito di Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Come distribuire un'applicazione in un cluster gestito di Service Fabric](tutorial-managed-cluster-deploy-app.md)

Questa parte della serie descrive come:

> [!div class="checklist"]
> * Dimensionare un nodo di un cluster gestito di Service Fabric

## <a name="prerequisites"></a>Prerequisiti

* Un cluster gestito di Service Fabric (vedere [*Distribuire un cluster gestito*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) o versione successiva (vedere [*Installare Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Dimensionare un cluster gestito di Service Fabric
Cambiare il numero di istanze per aumentare o diminuire il numero di nodi del tipo da dimensionare. È possibile trovare i nomi dei tipi di nodo nel modello di Azure Resource Manager della distribuzione del cluster o in Service Fabric Explorer.  

> [!NOTE]
> Se il tipo di nodo è primario, sarà necessario avere almeno 3 nodi per un cluster dello SKU Basic e 5 nodi per un cluster dello SKU Standard.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

L'aggiornamento del cluster verrà avviato automaticamente e dopo alcuni minuti saranno visibili i nodi aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

In questo passaggio è stato dimensionato un tipo di nodo in un cluster gestito di Service Fabric. Per altre informazioni sull'aggiunta e la rimozione di tipi di nodo, vedere:

> [!div class="nextstepaction"]
> [Aggiungere e rimuovere tipi di nodo in un cluster gestito di Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
