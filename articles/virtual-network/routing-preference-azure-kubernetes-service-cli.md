---
title: Configurare la preferenza di routing per un servizio Azure Kubernetes usando l'interfaccia della riga di comando di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come configurare un cluster del servizio AzureKs con preferenza di routing usando l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776574"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Configurare la preferenza di routing per un cluster Kubernetes usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come configurare la preferenza di routing tramite la rete ISP (opzione **Internet)** per un cluster Kubernetes usando l'interfaccia della riga di comando di Azure. La preferenza di routing viene impostata creando un indirizzo IP pubblico di tipo preferenza di routing **Internet**** e quindi usandolo durante la creazione del cluster del servizio Web AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.49 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse nell'area di Azure **Stati Uniti orientali**:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Creare un indirizzo IP pubblico con preferenza di routing **di tipo Internet** usando il comando [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

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

Il comando seguente crea il cluster del servizio Servizio Web Di seguito con l'indirizzo IP pubblico creato nella sezione precedente:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>La distribuzione del cluster del servizio Servizio Web Diaks richiede alcuni minuti.

Per la convalida, cercare l'indirizzo IP pubblico creato nel passaggio precedente in portale di Azure. Si noti che l'indirizzo IP è associato al servizio di bilanciamento del carico associato al cluster Kubernetes, come illustrato di seguito:

 ![Indirizzo IP pubblico delle preferenze di routing per Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [preferenza di routing per gli indirizzi IP pubblici](routing-preference-overview.md). 
- [Configurare la preferenza di routing per una macchina virtuale con l'interfaccia della riga di comando di Azure](configure-routing-preference-virtual-machine-cli.md).
