---
title: 'Esercitazione: Creare un gateway NAT - Interfaccia della riga di comando di Azure'
titlesuffix: Azure Virtual Network NAT
description: Introduzione alla creazione di un gateway NAT con l'interfaccia della riga di comando di Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: d312702f441cfe2ad94e347cadcdfc88d4cc2a72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479322"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Esercitazione: Creare un gateway NAT usando l'interfaccia della riga di comando di Azure

Questa esercitazione illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale.
> * Creare una macchina virtuale.
> * Creare un gateway NAT e associarlo alla rete virtuale.
> * Connettersi alla macchina virtuale e verificare l'indirizzo IP NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroupNAT** nella località **Stati Uniti orientali 2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

In questa sezione viene creato il gateway NAT e le risorse di supporto.

### <a name="create-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere a Internet, sono necessari uno o più indirizzi IP pubblici per il gateway NAT. Usare il comando [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) per creare una risorsa indirizzo IP pubblico denominata **myPublicIP** in **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Creare una risorsa gateway NAT

Creare un gateway NAT di Azure globale con [az network nat gateway create.](/cli/azure/network/nat#az_network_nat_gateway_create) Il risultato di questo comando creerà una risorsa gateway denominata **myNATgateway** che usa l'indirizzo IP pubblico **myPublicIP.** Il timeout di inattività è impostato su 10 minuti.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) nel gruppo di risorse **myResourceGroup.** Lo spazio indirizzi IP per la rete virtuale è **10.1.0.0/16.** La subnet all'interno della rete virtuale **è 10.1.0.0/24.**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Creare un host bastion

Creare un host Azure Bastion denominato **myBastionHost per** accedere alla macchina virtuale. 

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet Azure Bastion rete virtuale.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Creare un indirizzo IP pubblico per il bastion host [con az network public-ip create.](/cli/azure/network/public-ip#az_network_public_ip_create) 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare l'host bastion. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurare il servizio NAT per la subnet di origine

Verrà configurata la subnet di origine **mySubnet** nella rete virtuale **myVnet** per usare una risorsa gateway NAT specifica **myNATgateway** con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Questo comando attiverà il servizio NAT nella subnet specificata.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

A questo punto, tutto il traffico in uscita verso le destinazioni Internet usa il gateway NAT.  Non è necessario configurare una route definita dall'utente.


## <a name="virtual-machine"></a>Macchina virtuale

In questa sezione si creerà una macchina virtuale per testare il gateway NAT per verificare l'indirizzo IP pubblico della connessione in uscita.

Creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Attendere il completamento della creazione della macchina virtuale prima di passare alla sezione successiva.

## <a name="test-nat-gateway"></a>Testare il gateway NAT

In questa sezione verrà testato il gateway NAT. Prima di tutto si scoprirà l'indirizzo IP pubblico del gateway NAT. Ci si connetterà quindi alla macchina virtuale di test e si verificherà la connessione in uscita tramite il gateway NAT.
    
1. Accedere al [portale di Azure](https://portal.azure.com)

1. Trovare l'indirizzo IP pubblico per il gateway NAT nella **schermata Panoramica.** Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP**.

2. Prendere nota dell'indirizzo IP pubblico:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Individuare l'indirizzo IP pubblico del gateway NAT" border="true":::

3. Selezionare **Tutti i** servizi nel menu a sinistra, selezionare Tutte le risorse e quindi nell'elenco delle risorse selezionare **myVM** che si trova nel gruppo di risorse **myResourceGroupNAT.** 

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere **https://whatsmyip.com** nella barra degli indirizzi.

9. Verificare che l'indirizzo IP visualizzato corrisponda all'indirizzo del gateway NAT specificato nel passaggio precedente:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer ip esterno in uscita" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continuerà a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e il gateway NAT con la procedura seguente:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui NAT di rete virtuale di Azure, vedere:
> [!div class="nextstepaction"]
> [NAT di rete virtuale panoramica](nat-overview.md)
