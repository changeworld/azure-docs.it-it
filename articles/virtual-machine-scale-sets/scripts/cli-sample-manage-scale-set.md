---
title: Esempio di interfaccia della riga di comando di Azure
description: Questo esempio illustra come aggiungere dischi a un set di scalabilità di macchine virtuali. È possibile aggiornare i dischi e aggiungere le macchine virtuali a Azure AD l'autenticazione.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672606"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Creare e gestire un set di scalabilità di macchine virtuali

Usare questi comandi di esempio per prototipare un set di scalabilità di macchine virtuali usando l'interfaccia della riga di comando

Questi comandi di esempio illustrano le operazioni seguenti:

* Crea un set di scalabilità di macchine virtuali.
* Aggiungere e aggiornare dischi nuovi o esistenti a un set di scalabilità o a un'istanza del set.
* Aggiungere il set di scalabilità all'autenticazione Azure Active Directory (Azure AD).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Comandi di esempio

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Dopo aver aggiunto un nuovo disco dati, formattare e montare il disco. Per le macchine virtuali Windows, vedere la pagina relativa alla [connessione di un disco dati gestito a una macchina virtuale Windows tramite il portale di Azure](../../virtual-machines/windows/attach-managed-disk-portal.md). Per le macchine virtuali Linux, vedere [aggiungere un disco a una VM Linux](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Per utilizzare il disco espanso, espandere la partizione sottostante. Per altre informazioni, vedere [espandere una partizione disco e un file System](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

In questo esempio un disco dati è stato ridimensionato. È possibile usare la stessa procedura per aggiornare un disco del sistema operativo. Per ulteriori informazioni su una macchina virtuale Windows, vedere [come espandere l'unità del sistema operativo di una macchina virtuale](../../virtual-machines/windows/expand-os-disk.md). Per altre informazioni sulle macchine virtuali Linux, vedere [espandere i dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della](../../virtual-machines/linux/expand-disks.md)riga di comando di Azure.

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver usato questi comandi, eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Riferimenti CLI di Azure usati in questo articolo

* [AZ Disk Delete](/cli/azure/disk#az_disk_delete)
* [AZ disk List](/cli/azure/disk#az_disk_list)
* [AZ Disk Update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [AZ Virtual Machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [AZ vmss disk Detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [AZ vmss Identity Assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [AZ vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [AZ vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)