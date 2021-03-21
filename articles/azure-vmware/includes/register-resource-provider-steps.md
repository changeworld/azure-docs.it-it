---
title: Registrare il provider di risorse della soluzione Azure VMware
description: Procedura per registrare il provider di risorse della soluzione Azure VMware.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555859"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione. Per altre informazioni sui provider di risorse, vedere [provider e tipi di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portale](#tab/azure-portal)
 
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** nel menu del portale di Azure.

1. Nella casella **Tutti i servizi** immettere **sottoscrizione** e quindi selezionare **Sottoscrizioni**.

1. Selezionare la sottoscrizione dall'elenco per visualizzarla.

1. Selezionare **Provider di risorse** e immettere **Microsoft.AVS** nella ricerca. 
 
1. Se il provider di risorse non è registrato, selezionare **Registra**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare a usare interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Accedere alla sottoscrizione di Azure usata per la distribuzione della soluzione VMware di Azure tramite l'interfaccia della riga di comando di Azure. Registrare il `Microsoft.AVS` provider di risorse con il comando [AZ provider Register](/cli/azure/provider#az_provider_register) :

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

È possibile usare il comando [AZ provider list](/cli/azure/provider#az_provider_list) per visualizzare tutti i provider disponibili.

---


 
