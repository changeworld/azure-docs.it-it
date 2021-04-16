---
title: Come contrassegnare una macchina virtuale di Azure usando l'interfaccia della riga di comando di Azure
description: Informazioni sull'assegnazione di tag a una macchina virtuale tramite l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20bb4ab622a01646bcc61d0f691c514a25a06edc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502607"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Come contrassegnare una macchina virtuale usando l'interfaccia della riga di comando di Azure

Questo articolo descrive come contrassegnare una macchina virtuale usando l'interfaccia della riga di comando di Azure. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. È anche possibile contrassegnare una macchina virtuale usando Azure [PowerShell.](tag-powershell.md)


È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, inclusi i tag , usando `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Per aggiungere un nuovo tag vm tramite l'interfaccia della riga di comando di Azure, è possibile usare il `azure vm update` comando insieme al parametro tag `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Per rimuovere i tag, è possibile usare `--remove` il parametro nel comando `azure vm update` .

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Ora che sono stati applicati tag alle risorse dell'interfaccia della riga di comando di Azure e al portale, esaminare i dettagli di utilizzo per visualizzare i tag nel portale di fatturazione.

### <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).
- Per informazioni su come i tag consentono di gestire l'uso delle risorse di Azure, vedere [Informazioni sulla fattura di Azure.](../cost-management-billing/understand/review-individual-bill.md)
