---
title: Creare un gruppo di posizionamento di prossimità usando l'interfaccia della riga di comando di Azure
description: Informazioni sulla creazione e sull'uso di gruppi di posizionamento di prossimità per le macchine virtuali in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771444"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando l'interfaccia della riga di comando di Azure

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è necessario distribuirle all'interno di un gruppo [di posizionamento di prossimità.](../co-location.md#proximity-placement-groups)

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino una vicino all'altra. I gruppo di posizionamento di prossimità sono utili per i carichi di lavoro che richiedono una latenza ridotta.


## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento di prossimità
Creare un gruppo di posizionamento di prossimità usando [`az ppg create`](/cli/azure/ppg#az_ppg_create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Elencare i gruppi di posizionamento di prossimità

È possibile elencare tutti i gruppi di posizionamento di prossimità [usando az ppg list](/cli/azure/ppg#az_ppg_list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale all'interno del gruppo di posizionamento di prossimità [usando il nuovo comando az vm](/cli/azure/vm#az_vm_create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

È possibile visualizzare la macchina virtuale nel gruppo di posizionamento di prossimità [usando az ppg show](/cli/azure/ppg#az_ppg_show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo stesso parametro con `--ppg` [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) per creare un set di disponibilità e tutte le macchine virtuali nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

## <a name="scale-sets"></a>Set di scalabilità

È anche possibile creare un set di scalabilità nel gruppo di posizionamento di prossimità. Usare lo stesso `--ppg` parametro con [az vmss create](/cli/azure/vmss#az_vmss_create) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui comandi [dell'interfaccia della riga di comando](/cli/azure/ppg) di Azure per i gruppi di posizionamento di prossimità.