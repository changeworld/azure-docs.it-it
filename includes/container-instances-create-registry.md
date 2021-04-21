---
title: includere file
description: includere file
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 8d4178ea1f6f12def938dfc91e7ae0e6ee3b738c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786920"
---
## <a name="create-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

Prima di creare il registro contenitori, è necessario un *gruppo di risorse* in cui eseguirne la distribuzione. Un gruppo di risorse è una raccolta logica in cui vengono distribuite e gestite tutte le risorse di Azure.

Creare un gruppo di risorse con il comando [az group create][az-group-create]. Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Dopo aver creato il gruppo di risorse, creare un'istanza di Registro Azure Container con il comando [az acr create][az-acr-create]. Il nome del registro contenitori deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Sostituire `<acrName>` con un nome univoco per il registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Ecco l'output parziale per una nuova istanza di Registro Azure Container denominata *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Nel resto dell'esercitazione viene usato `<acrName>` come segnaposto per il nome del registro contenitori scelto in questo passaggio.

## <a name="log-in-to-container-registry"></a>Accedere al registro contenitori

È necessario accedere all'istanza di Registro Azure Container prima di eseguire il push di immagini. Usare il comando [az acr login][az-acr-login] per completare l'operazione. È necessario specificare il nome univoco scelto per il registro contenitori al momento della creazione.

```azurecli
az acr login --name <acrName>
```

Ad esempio:

```azurecli
az acr login --name mycontainerregistry082
```

Al termine, il comando restituisce `Login Succeeded`:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
