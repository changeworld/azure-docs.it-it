---
title: Registrare il provider di risorse della soluzione Azure VMware
description: Procedura per registrare il provider di risorse della soluzione Azure VMware.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653169"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione. Per altre informazioni sui provider di risorse, vedere [provider e tipi di risorse di Azure](/azure/azure-resource-manager/management/resource-providers-and-types).

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
