---
title: Creare un gruppo di posizionamento di prossimità usando l'interfaccia della riga di comando di Azure
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per le macchine virtuali in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: a347c9284608340811f9c2388df26129baeb8837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505648"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando l'interfaccia della riga di comando di Azure

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](../co-location.md#proximity-placement-groups).

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino una vicino all'altra. I gruppo di posizionamento di prossimità sono utili per i carichi di lavoro che richiedono una latenza ridotta.


## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento vicino
Creare un gruppo di posizionamento di prossimità usando [`az ppg create`](/cli/azure/ppg#az-ppg-create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Elencare i gruppi di posizionamento di prossimità

È possibile elencare tutti i gruppi di posizionamento vicini usando [AZ PPG list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale all'interno del gruppo di posizionamento di prossimità usando il [nuovo AZ VM](/cli/azure/vm#az-vm-create).

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

È possibile visualizzare la macchina virtuale nel gruppo posizionamento prossimità usando [AZ PPG Show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo stesso `--ppg` parametro con il comando [AZ VM Availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) per creare un set di disponibilità e tutte le VM nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

## <a name="scale-sets"></a>Set di scalabilità

È anche possibile creare un set di scalabilità nel gruppo di posizionamento di prossimità. Usare lo stesso `--ppg` parametro con il comando [AZ vmss create](/cli/azure/vmss#az_vmss_create) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui comandi dell'interfaccia della riga di comando di [Azure](/cli/azure/ppg) per i gruppi di posizionamento prossimità.