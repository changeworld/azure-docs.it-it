---
title: Modificare le prestazioni di Azure Managed disks-CLI/PowerShell
description: Informazioni su come modificare i livelli di prestazioni per i dischi gestiti esistenti usando il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 161aafce1c04e5d09cf08529bcbf1baf6b8a86b1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674926"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Modificare il livello di prestazioni usando il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Creare un disco dati vuoto con un livello superiore rispetto al livello di base

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Creare un disco del sistema operativo con un livello superiore rispetto al livello di base da un'immagine di Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>Aggiornare il livello di un disco

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>Mostra il livello di un disco

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>Modificare il livello di prestazioni di un disco senza tempi di inattività (anteprima)

È anche possibile modificare il livello di prestazioni senza tempi di inattività, quindi non è necessario deallocare la VM o scollegare il disco per modificare il livello. Per ulteriori informazioni e il collegamento per l'iscrizione per l'anteprima, vedere la sezione [modifica del livello di prestazioni senza tempi di inattività (anteprima)](#changing-performance-tier-without-downtime-preview) .


Lo script seguente aggiornerà il livello di un disco in un livello superiore rispetto al livello Baseline usando il modello di esempio [CreateUpdateDataDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json). Sostituire `<yourSubScriptionID>` ,,, `<yourResourceGroupName>` `<yourDiskName>` `<yourDiskSize>` , `<yourDesiredPerformanceTier>` quindi eseguire lo script:

 ```cli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
diskName=<yourDiskName>
diskSize=<yourDiskSize>
performanceTier=<yourDesiredPerformanceTier>
region=EastUS2EUAP

 az login

 az account set --subscription $subscriptionId

 az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
--parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
```

Per il completamento di una modifica del livello di prestazioni possono essere necessari fino a 15 minuti. Per verificare che i livelli del disco siano stati modificati, usare il comando seguente:

```cli
az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-12-01 --query [properties.tier] -o tsv
```

## <a name="next-steps"></a>Passaggi successivi

Se è necessario ridimensionare un disco per sfruttare i livelli di prestazioni più elevati, vedere questi articoli:

- [Espandere dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](linux/expand-disks.md)
- [Espandere un disco gestito collegato a una macchina virtuale Windows](windows/expand-os-disk.md)
