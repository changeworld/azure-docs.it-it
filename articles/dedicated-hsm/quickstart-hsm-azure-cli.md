---
title: "Avvio rapido: Creare un modulo HSM dedicato di Azure con l'interfaccia della riga di comando di Azure"
description: Creare, visualizzare, elencare, aggiornare ed eliminare moduli HSM dedicati di Azure usando l'interfaccia della riga di comando di Azure.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020856"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Avvio rapido: Creare un modulo HSM dedicato di Azure con l'interfaccia della riga di comando di Azure

Questo articolo descrive come creare e gestire un modulo HSM dedicato di Azure usando l'estensione [az dedicated-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm) dell'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free/).
  
  Se si hanno più sottoscrizioni di Azure, impostare quella da usare per la fatturazione con il comando [az account set](/cli/azure/account#az_account_set) dell'interfaccia della riga di comando di Azure.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Tutti i requisiti soddisfatti per un modulo HSM dedicato, come la registrazione, l'approvazione, nonché una rete virtuale e una macchina virtuale da usare per il provisioning. Per altre informazioni su requisiti e prerequisiti dei moduli HSM dedicati, vedere [Esercitazione: Distribuzione di moduli di protezione hardware in una rete virtuale esistente usando l'interfaccia della riga di comando di Azure](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) è un contenitore logico per la distribuzione e la gestione delle risorse di Azure come gruppo. Se non è già disponibile un gruppo di risorse per il modulo HSM dedicato, crearne uno con il comando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato `myRG` nell'area di Azure `westus`:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Creare un modulo HSM dedicato

Per creare un modulo HSM dedicato, usare il comando [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create). L'esempio seguente effettua il provisioning di un modulo HSM dedicato denominato `hsm1` nell'area `westus`, nel gruppo di risorse `myRG` e nella sottoscrizione, nella rete virtuale e nella subnet specificate. I parametri obbligatori sono `name`, `location` e `resource group`.

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

La distribuzione richiede all'incirca 25-30 minuti.

## <a name="get-a-dedicated-hsm"></a>Recuperare un modulo HSM dedicato

Per recuperare un modulo HSM dedicato corrente, eseguire il comando [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show). L'esempio seguente recupera il modulo HSM dedicato `hsm1` nel gruppo di risorse `myRG`.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Aggiornare un modulo HSM dedicato

Usare il comando [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) per aggiornare un modulo HSM dedicato. L'esempio seguente aggiorna il modulo HSM dedicato `hsm1` nel gruppo di risorse `myRG` e i relativi tag:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Elencare i moduli HSM dedicati

Eseguire il comando [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) per ottenere informazioni sui moduli HSM dedicati correnti. L'esempio seguente elenca i moduli HSM dedicati nel gruppo di risorse `myRG`:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Rimuovere un modulo HSM dedicato

Per rimuovere un modulo HSM dedicato, usare il comando [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete). L'esempio seguente elimina il modulo HSM dedicato `hsm1` dal gruppo di risorse `myRG`:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

Se il gruppo di risorse creato per il modulo HSM dedicato non è più necessario, è possibile eliminarlo eseguendo il comando [az group delete](/cli/azure/group#az_group_delete). Questo comando elimina il gruppo e tutte le risorse al suo interno, incluse quelle non correlati al modulo HSM dedicato. L'esempio seguente elimina il gruppo di risorse `myRG` e tutto il relativo contenuto:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo HSM dedicato di Azure, vedere [Modulo HSM dedicato di Azure](overview.md).
