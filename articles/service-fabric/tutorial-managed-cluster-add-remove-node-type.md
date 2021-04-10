---
title: Aggiungere e rimuovere tipi di nodo in un cluster gestito di Service Fabric (anteprima)
description: Questa esercitazione illustra come aggiungere e rimuovere tipi di nodo di un cluster gestito di Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791241"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Esercitazione: Aggiungere e rimuovere tipi di nodo da un cluster gestito di Service Fabric (anteprima)

In questa serie di esercitazioni verrà illustrato quanto segue:

> [!div class="checklist"]
> * [Come distribuire un cluster gestito di Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Come aumentare il numero di istanze di un cluster gestito di Service Fabric](tutorial-managed-cluster-scale.md)
> * Come aggiungere e rimuovere nodi in un cluster gestito di Service Fabric
> * [Come distribuire un'applicazione in un cluster gestito di Service Fabric](tutorial-managed-cluster-deploy-app.md)

Questa parte della serie descrive come:

> [!div class="checklist"]
> * Aggiungere un tipo di nodo a un cluster gestito di Service Fabric
> * Eliminare un tipo di nodo da un cluster gestito di Service Fabric

## <a name="prerequisites"></a>Prerequisiti

* Un cluster gestito di Service Fabric (vedere [*Distribuire un cluster gestito*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) o versione successiva (vedere [*Installare Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Aggiungere un tipo di nodo a un cluster gestito di Service Fabric

È possibile aggiungere un tipo di nodo a un cluster gestito Service Fabric tramite un modello di Azure Resource Manager, PowerShell o l'interfaccia della riga di comando. In questa esercitazione verrà aggiunto un tipo di nodo tramite Azure PowerShell.

Per creare un nuovo tipo di nodo, è necessario definire tre proprietà:

* **Nome del tipo di nodo**: nome univoco rispetto a qualsiasi tipo di nodo esistente nel cluster.
* **Numero di istanze**: numero iniziale di nodi del nuovo tipo.
* **Dimensioni macchina virtuale**: SKU di VM per i nodi. Se non specificato, viene usato il valore predefinito *Standard_D2*.

> [!NOTE]
> Se il tipo di nodo aggiunto è il primo o l'unico nel cluster, è necessario usare la proprietà Primary.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Rimuovere un tipo di nodo da un cluster gestito di Service Fabric

Per rimuovere un tipo di nodo da un cluster gestito di Service Fabric, è necessario usare PowerShell o l'interfaccia della riga di comando. In questa esercitazione verrà rimosso un tipo di nodo tramite Azure PowerShell.

> [!NOTE]
> Non è possibile rimuovere un tipo di nodo primario se è l'unico tipo di nodo primario nel cluster.  

Per rimuovere un tipo di nodo:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Passaggi successivi

 In questa sezione sono stati aggiunti ed eliminati i tipi di nodo. Per informazioni su come distribuire un'applicazione in un cluster gestito di Service Fabric, vedere:

> [!div class="nextstepaction"]
> [Distribuire un'app in un cluster gestito di Service Fabric](tutorial-managed-cluster-deploy-app.md)
