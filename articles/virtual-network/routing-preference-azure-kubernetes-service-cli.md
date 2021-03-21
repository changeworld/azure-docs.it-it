---
title: Configurare la preferenza di routing per un servizio Kubernetes di Azure usando l'interfaccia della riga di comando
titlesuffix: Azure Virtual Network
description: Informazioni su come configurare un cluster AKS con preferenza di routing usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679818"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Configurare la preferenza di routing per un cluster Kubernetes usando l'interfaccia della riga di comando

Questo articolo illustra come configurare le preferenze di routing tramite la rete ISP (opzione **Internet** ) per un cluster Kubernetes usando l'interfaccia della riga di comando di Azure. Per impostare le preferenze di routing, è necessario creare un indirizzo IP pubblico di tipo preferenza di routing * * Internet * * * * e quindi usarlo durante la creazione del cluster AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.49 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse nell'area di Azure **Stati Uniti orientali**:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Creare un indirizzo IP pubblico con preferenza di routing di tipo **Internet** usando il comando [AZ Network Public-IP create](/cli/azure/network/public-ip#az-network-public-ip-create).

Il comando seguente crea un nuovo indirizzo IP pubblico con preferenza di routing **Internet** nell'area di Azure **Stati Uniti orientali**.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  Attualmente, la preferenza di routing supporta solo indirizzi IP pubblici IPV4.

## <a name="get-the-id-of-public-ip-address"></a>Ottenere l'ID dell'indirizzo IP pubblico

Il comando seguente restituisce l'ID dell'indirizzo IP pubblico creato nella sezione precedente:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Creare un cluster Kubernetes con l'indirizzo IP pubblico

Il comando seguente crea il cluster AKS con l'indirizzo IP pubblico creato nella sezione precedente:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Per la distribuzione del cluster AKS sono necessari alcuni minuti.

Per convalidare, cercare l'IP pubblico creato nel passaggio precedente in portale di Azure, si noterà che l'indirizzo IP è associato al servizio di bilanciamento del carico associato al cluster Kubernetes, come illustrato di seguito:

 ![Indirizzo IP pubblico preferenza di routing per Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [preferenza di routing per gli indirizzi IP pubblici](routing-preference-overview.md). 
- [Configurare la preferenza di routing per una macchina virtuale con l'interfaccia della riga di comando di Azure](configure-routing-preference-virtual-machine-cli.md).

