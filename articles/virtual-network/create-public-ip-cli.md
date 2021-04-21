---
title: Creare un indirizzo IP pubblico - Interfaccia della riga di comando di Azure
description: Informazioni su come creare un indirizzo IP pubblico con l'interfaccia della riga di comando di Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767692"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Guida introduttiva: Creare un indirizzo IP pubblico con l'interfaccia della riga di comando di Azure

Questo articolo illustra come creare una risorsa indirizzo IP pubblico usando l'interfaccia della riga di comando di Azure. Per altre informazioni sulle risorse a cui può essere associato, sulla differenza tra SKU Basic e Standard e altre informazioni correlate, vedere [Indirizzi IP pubblici](./public-ip-addresses.md).  Per questo esempio, ci si concentrerà solo sugli indirizzi IPv4. Per altre informazioni sugli indirizzi IPv6, vedere [IPv6 per la rete virtuale di Azure.](./ipv6-overview.md)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse [con az group create](/cli/azure/group#az_group_create) denominato **myResourceGroup** nella **località eastus2.**

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Creare un indirizzo IP pubblico

---
# <a name="standard-sku---using-zones"></a>[**SKU standard - Uso delle zone**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Il comando seguente funziona per l'API versione 2020-08-01 o successiva.  Per altre informazioni sulla versione dell'API attualmente in uso, vedere Provider [di risorse e tipi](../azure-resource-manager/management/resource-providers-and-types.md).

Usare [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) per creare un indirizzo IP pubblico con ridondanza della zona standard denominato **myStandardZRPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> Per le versioni dell'API precedenti alla versione 2020-08-01, eseguire il comando precedente senza specificare un parametro di zona per creare un indirizzo IP con ridondanza della zona. 
>

Per creare un indirizzo IP pubblico di zona standard in Zona 2 **denominato myStandardZonalPublicIP** in **myResourceGroup,** usare il comando seguente:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Si noti che le opzioni precedenti per le zone sono solo selezioni valide nelle aree con [zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU standard - Nessuna zona**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Il comando seguente funziona per l'API versione 2020-08-01 o successiva.  Per altre informazioni sulla versione dell'API attualmente in uso, vedere Provider [di risorse e tipi](../azure-resource-manager/management/resource-providers-and-types.md).

Usare [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) per creare un indirizzo IP pubblico standard come risorsa non di zona denominata **myStandardPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Questa selezione è valida in tutte le aree ed è la selezione predefinita per gli indirizzi IP pubblici standard nelle aree [senza zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-create-public-ip-basic)

Usare [az network public-ip create per](/cli/azure/network/public-ip#az_network_public_ip_create) creare un indirizzo IP pubblico statico di base denominato **myBasicPublicIP** in **myResourceGroup**.  Gli IP pubblici di base non hanno il concetto di zone di disponibilità.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Se è accettabile che l'indirizzo IP cambi nel **tempo,** è possibile selezionare l'assegnazione ip dinamico modificando il metodo di allocazione in "Dinamico".

---

## <a name="additional-information"></a>Informazioni aggiuntive 

Per altri dettagli sulle singole variabili elencate in precedenza, vedere [Gestire gli indirizzi IP pubblici.](./virtual-network-public-ip-address.md#create-a-public-ip-address)

## <a name="next-steps"></a>Passaggi successivi
- Associare [un indirizzo IP pubblico a una macchina virtuale.](./associate-public-ip-address-vm.md#azure-portal)
- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
