---
title: Scambiare tra dischi del sistema operativo usando l'interfaccia della riga di comando di Azure
description: Modificare il disco del sistema operativo usato da una macchina virtuale di Azure usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85d6350a36e62ace8f1922d30493d0f1d448d315
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765936"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Modificare il disco del sistema operativo usato da una macchina virtuale di Azure usando l'interfaccia della riga di comando di Azure


Se si dispone di una macchina virtuale esistente, ma si desidera scambiare il disco con un disco di backup o un altro disco del sistema operativo, è possibile usare l'interfaccia della riga di comando di Azure per scambiare i dischi del sistema operativo. Non è necessario eliminare e ricreare la macchina virtuale. È anche possibile usare un disco gestito in un altro gruppo di risorse, purché non sia già in uso.

La macchina virtuale deve essere arrestata\deallocata, quindi l'ID risorsa del disco gestito può essere sostituito con l'ID risorsa di un altro disco gestito. 

Assicurarsi che il tipo di archiviazione e le dimensioni della macchina virtuale siano compatibili con il disco che si intende collegare. Ad esempio, se il disco che si vuole usare si trova in Archiviazione Premium, la macchina virtuale deve essere idonea per Archiviazione Premium (ad esempio con le dimensioni della serie DS).

Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0.25 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 


Usare [az disk list](/cli/azure/disk) per ottenere un elenco dei dischi nel gruppo di risorse.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Usare [az vm stop](/cli/azure/vm) per arrestare\deallocare la macchina virtuale prima di effettuare lo scambio dei dischi.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Usare [az vm update](/cli/azure/vm#az_vm_update) con l'ID risorsa completo del nuovo disco per il parametro `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Avviare la macchina virtuale con [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Passaggi successivi**

Per creare una copia di un disco, vedere [Snapshot di un disco](snapshot-copy-managed-disk.md).
