---
title: Ridistribuire macchine virtuali Linux in Azure | Documentazione Microsoft
description: Come ridistribuire macchine virtuali Linux in Azure per ridurre i problemi di connessione SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d553fb6b2061f987e3e098ae47ebca9cd3f60984
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203406"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Ridistribuire una macchina virtuale Linux in un nuovo nodo di Azure
Se si riscontrano difficoltà nella risoluzione dei problemi relativi a SSH o all'accesso delle applicazioni a una macchina virtuale Linux in Azure, potrebbe essere utile ridistribuire la VM. Quando si ridistribuisce una VM, quest'ultima viene spostata su un nuovo nodo dell'infrastruttura di Azure, quindi viene riattivata. Tutte le opzioni di configurazione e le risorse associate vengono mantenute. In questo articolo viene illustrato come ridistribuire una VM con l'interfaccia della riga di comando di Azure o il portale di Azure.

> [!NOTE]
> Dopo la ridistribuzione di una VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati. 


## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
Installare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e accedere all'account di Azure con il comando [az login](/cli/azure/reference-index).

Ridistribuire la VM con il comando [az vm redeploy](/cli/azure/vm). Nell'esempio seguente la VM denominata *myVM* viene ridistribuita nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Usare l'interfaccia della riga di comando classica di Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Installare la [versione più recente dell'interfaccia della riga di comando classica di Azure](/cli/azure/install-classic-cli) e accedere all'account di Azure. Verificare di aver attivato la modalità Resource Manager (`azure config mode arm`).

Nell'esempio seguente la VM denominata *myVM* viene ridistribuita nel gruppo di risorse denominato *myResourceGroup*:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passaggi successivi
In caso di problemi di connessione alla VM, è possibile trovare assistenza specifica sulla [risoluzione dei problemi relativi alle connessioni SSH](troubleshoot-ssh-connection.md) o sui [passaggi dettagliati per la risoluzione dei problemi SSH](detailed-troubleshoot-ssh-connection.md). Se non si riesce ad accedere a un'applicazione in esecuzione sulla VM, è possibile leggere l'articolo sulle [difficoltà nella risoluzione dei problemi relativi alle applicazioni](troubleshoot-app-connection.md).
