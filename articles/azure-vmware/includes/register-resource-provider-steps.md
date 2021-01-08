---
title: Registrare il provider di risorse della soluzione Azure VMware
description: Procedura per registrare il provider di risorse della soluzione Azure VMware.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770826"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione.  

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