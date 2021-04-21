---
title: includere file
description: includere file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/06/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c059746262cdaf2901c765d3ce3848887b2e629c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781142"
---
Nell'output del comando la sezione `identity` mostra che nell'attività è impostata l'identità di tipo `SystemAssigned`. `principalId` è l'ID entità dell'identità dell'attività:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Usare il comando [az acr task show][az-acr-task-show] per archiviare il valore di principalId in una variabile, da usare nei comandi successivi. Sostituire il nome dell'attività e del registro con i propri valori nel comando seguente:

```azurecli
principalID=$(az acr task show \
  --name <task_name> --registry <registry_name> \
  --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
