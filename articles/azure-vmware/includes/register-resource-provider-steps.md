---
title: Registrare il provider di risorse della soluzione Azure VMware
description: Procedura per registrare il provider di risorse della soluzione Azure VMware.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750154"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione. Per altre informazioni sui provider di risorse, vedere [provider e tipi di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Portale di Azure
 
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** nel menu del portale di Azure.

1. Nella casella **Tutti i servizi** immettere **sottoscrizione** e quindi selezionare **Sottoscrizioni**.

1. Selezionare la sottoscrizione dall'elenco per visualizzarla.

1. Selezionare **Provider di risorse** e immettere **Microsoft.AVS** nella ricerca. 
 
1. Se il provider di risorse non è registrato, selezionare **Registra**.