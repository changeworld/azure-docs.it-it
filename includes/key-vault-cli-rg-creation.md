---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070280"
---
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Usare il comando [AZ Group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse denominato *myResourceGroup* nella località *eastus* .

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
