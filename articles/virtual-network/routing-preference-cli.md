---
title: Configurare la preferenza di routing per un indirizzo IP pubblico con l'interfaccia della riga di comando di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come creare un indirizzo IP pubblico con una preferenza di routing del traffico Internet usando l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: 58b91c105ed48617b64356904942f5ab6b461cda
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776556"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configurare la preferenza di routing per un indirizzo IP pubblico con l'interfaccia della riga di comando di Azure

Questo articolo illustra come configurare una preferenza di routing tramite rete dell'ISP (opzione **Internet**) per un indirizzo IP pubblico con l'interfaccia della riga di comando di Azure. Dopo aver creato l'indirizzo IP pubblico, è possibile associarlo alle risorse di Azure seguenti per il traffico Internet in ingresso e in uscita:

* Macchina virtuale
* Set di scalabilità di macchine virtuali
* Servizio Azure Kubernetes
* Servizio di bilanciamento del carico Internet
* Gateway applicazione
* Firewall di Azure

Per impostazione predefinita, la preferenza di routing per l'indirizzo IP pubblico è impostata sulla rete globale Microsoft per tutti i servizi di Azure e può essere associata a qualsiasi servizio di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.49 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse nell'area di Azure **Stati Uniti orientali**:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Creare un indirizzo IP pubblico con preferenza di routing di **tipo Internet** usando il comando [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), con il formato illustrato di seguito.

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

È possibile associare l'indirizzo IP pubblico creato in precedenza a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Attenersi alla sezione dell'interfaccia della riga di comando nella pagina dell'esercitazione: [Associare un indirizzo IP pubblico a una macchina virtuale](associate-public-ip-address-vm.md#azure-cli) per associare l'indirizzo IP pubblico alla macchina virtuale. È anche possibile associare l'indirizzo IP pubblico creato con un'istanza di [Azure Load Balancer](../load-balancer/load-balancer-overview.md), assegnandolo alla configurazione **front-end** del servizio di bilanciamento del carico. L'indirizzo IP pubblico viene usato come indirizzo IP virtuale (indirizzo VIP) di bilanciamento del carico.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [preferenza di routing per gli indirizzi IP pubblici](routing-preference-overview.md). 
- [Configurare la preferenza di routing per una macchina virtuale con l'interfaccia della riga di comando di Azure](configure-routing-preference-virtual-machine-cli.md).
