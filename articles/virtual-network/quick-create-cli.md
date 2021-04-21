---
title: 'Creare una rete virtuale: guida introduttiva - Interfaccia della riga di comando di Azure'
titlesuffix: Azure Virtual Network
description: In questa guida introduttiva si apprenderà a creare una rete virtuale usando l'interfaccia della riga di comando di Azure. Una rete virtuale consente alle risorse di Azure di comunicare tra loro e con Internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776754"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Avvio rapido: Creare una rete virtuale usando l'interfaccia della riga di comando di Azure

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. 

Questa guida introduttiva illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Si effettua quindi la connessione alle VM da Internet e si stabilisce una comunicazione privata tramite la nuova rete virtuale.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). Questo esempio crea un gruppo di risorse **denominato CreateVNetQS-rg** nella **località Eastus:**

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Questo esempio crea una rete virtuale predefinita denominata **myVNet** con una subnet denominata **default:**

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). 

Se le chiavi SSH non esistono già in una posizione predefinita, vengono create dal comando. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. 

L'opzione `--no-wait` consente di creare la macchina virtuale in background. È possibile continuare con il passaggio successivo. 

Questo esempio crea una macchina virtuale denominata **myVM1:**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

È stata usata `--no-wait` l'opzione nel passaggio precedente. È possibile procedere e creare la seconda macchina virtuale denominata **myVM2.**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Messaggio di output dell'interfaccia della riga di comando di Azure

La creazione delle macchine virtuali può richiedere alcuni minuti. Al termine della creazione delle VM in Azure, l'interfaccia della riga di comando di Azure restituisce un output simile al seguente:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>IP pubblico della macchina virtuale

Per ottenere l'indirizzo IP pubblico **myVM2,** usare [az network public-ip show:](/cli/azure/network/public-ip#az_network_public_ip_show)

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

In questo comando sostituire con `<publicIpAddress>` l'indirizzo IP pubblico della **macchina virtuale myVM2:**

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

Per confermare la comunicazione privata tra le macchine virtuali **myVM2** **e myVM1,** immettere questo comando:

```bash
ping myVM1 -c 4
```

Si riceveranno quattro risposte da *10.0.0.4*.

Uscire dalla sessione SSH con la **macchina virtuale myVM2.**

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido: 

* Sono state create una rete virtuale predefinita e due macchine virtuali. 
* È stata effettuata la connessione a una VM da Internet ed è stata stabilita una comunicazione privata tra le due VM.

La comunicazione privata tra macchine virtuali non è limitata in una rete virtuale. 

Per altre informazioni sulla configurazione di diversi tipi di comunicazioni di rete delle VM, passare all'articolo successivo:
> [!div class="nextstepaction"]
> [Filtrare il traffico di rete](tutorial-filter-network-traffic.md)
