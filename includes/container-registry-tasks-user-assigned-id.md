---
title: includere file
description: includere file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d81b6f5367efa92c9249956faa058441edf98561
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755641"
---
### <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Creare un'identità denominata *myACRTasksId* nella sottoscrizione usando il comando [az identity create][az-identity-create]. È possibile usare lo stesso gruppo di risorse usato in precedenza per creare il registro contenitori oppure uno diverso.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Per configurare l'identità nei passaggi seguenti, usare il comando [az identity show][az-identity-show] per archiviare l'ID risorsa, l'ID entità e l'ID client in variabili.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
