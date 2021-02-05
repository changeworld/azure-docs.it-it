---
title: Esempio dell'interfaccia della riga di comando di Azure per lo sfondo di una macchina virtuale di Azure Marketplace
description: Esempio dell'interfaccia della riga di comando di Azure per lo trasferimento di una macchina virtuale di Azure Marketplace in una sottoscrizione diversa
author: cynthn
ms.author: cynthn
manager: ''
ms.date: 01/29/2021
ms.topic: sample
ms.service: virtual-machines
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2fdb968d5bc8b13dad995b30942ce9beb67e37e7
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580804"
---
# <a name="move-a-marketplace-azure-virtual-machine-to-another-subscription"></a>Spostare una macchina virtuale di Azure del Marketplace in un'altra sottoscrizione

Per spostare una macchina virtuale del Marketplace in una sottoscrizione diversa, è necessario spostare il disco del sistema operativo nella sottoscrizione e quindi ricreare la macchina virtuale.

Questa procedura non è necessaria per spostare un disco dati in una nuova sottoscrizione. Al contrario, creare una nuova macchina virtuale nella nuova sottoscrizione dal Marketplace, quindi spostare e aggiungere il disco dati.

Questo script illustra tre operazioni:

- Creare uno snapshot di un disco del sistema operativo.
- Spostare lo snapshot in una sottoscrizione diversa.
- Creare una macchina virtuale in base a tale snapshot.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-script"></a>Script di esempio

Per spostare una macchina virtuale del Marketplace in una sottoscrizione diversa, è necessario creare una nuova macchina virtuale per la stessa offerta del Marketplace dal disco del sistema operativo spostato.

> [!NOTE]
> Se il piano della macchina virtuale non è più disponibile nel Marketplace, non è possibile usare questa procedura.

```azurecli
#!/bin/bash
# Set variable values before proceeding. 

# Variables
sourceResourceGroup= Resource group for the current virtual machine
sourceSubscription= Subscription for the current virtual machine
vmName= Name of the current virtual machine

destinationResourceGroup= Resource group for the new virtual machine, create if necessary
destinationSubscription= Subscription for the new virtual machine

# Set your current subscription for the source virtual machine
az account set --subscription $sourceSubscription

# Load variables about your virtual machine
# osType = windows or linux
osType=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --subscription $sourceSubscription \
    --query 'storageProfile.osDisk.osType' --output tsv)

# offer = Your offer in Marketplace
offer=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.offer' --output tsv)

# plan = Your plan in Marketplace
plan=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'plan' --output tsv)

# publisher = Your publisher in Marketplace
publisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.publisher' --output tsv)

# Get information to create new virtual machine
planName=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.name' --name $vmName)
planProduct=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.product' --name $vmName)
planPublisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.publisher' --name $vmName)

# Get the name of the OS disk
osDiskName=$(az vm show --resource-group $sourceResourceGroup --name $vmName \
    --query 'storageProfile.osDisk.name' --output tsv)

# Verify the terms for your market virtual machine
az vm image terms show --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $sourceSubscription

# Deallocate the virtual machine
az vm deallocate --resource-group $sourceResourceGroup --name $vmName

# Create a snapshot of the OS disk
az snapshot create --resource-group $sourceResourceGroup --name MigrationSnapshot \
    --source "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/disks/$osDiskName"

# Move the snapshot to your destination resource group
az resource move --destination-group $destinationResourceGroup \
    --destination-subscription-id $destinationSubscription \
    --ids "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot"

# Set your subscription to the destination value
az account set --subscription $destinationSubscription

# Accept the terms from the Marketplace
az vm image terms accept --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $destinationSubscription

# Create disk from the snapshot 
az disk create --resource-group $destinationResourceGroup --name DestinationDisk \
    --source "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot" \
    --os-type $osType

# Create virtual machine from disk
az vm create --resource-group $destinationResourceGroup --name $vmName \
    --plan-name $planName --plan-product $planProduct  --plan-publisher $planPublisher \
    --attach-os-disk "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/disks/DestinationDisk" \
    --os-type $osType
```

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo l'esecuzione dell'esempio, usare i comandi seguenti per rimuovere i gruppi di risorse e tutte le risorse associate:

```azurecli
az group delete --name $sourceResourceGroup --subscription $sourceSubscription
az group delete --name $destinationResourceGroup --subscription $destinationSubscription
```

## <a name="azure-cli-references-used-in-this-article"></a>Riferimenti CLI di Azure usati in questo articolo

- [az account set](/cli/azure/account#az_account_set)
- [az disk create](/cli/azure/disk#az_disk_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ Resource Move](/cli/azure/resource#az_resource_move)
- [az snapshot create](/cli/azure/snapshot#az_snapshot_create)
- [az vm create](/cli/azure/vm#az_vm_create)
- [AZ VM deallocate](/cli/azure/vm#az_vm_deallocate)
- [az vm delete](/cli/azure/vm#az_vm_delete)
- [AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view)
- [AZ VM Image termini Accept](/cli/azure/vm/image/terms#az_vm_image_terms_accept)
- [AZ VM Image terms Show](/cli/azure/vm/image/terms#az_vm_image_terms_show)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>Passaggi successivi

- [Spostare le VM in un'altra area di Azure](../site-recovery/azure-to-azure-tutorial-migrate.md)
- [Spostare una macchina virtuale in un'altra sottoscrizione o gruppo di risorse](/linux/move-vm.md)
