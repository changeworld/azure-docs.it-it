---
title: Abilitare l'espansione del disco su richiesta
description: Abilitare l'espansione del disco su richiesta sul disco gestito.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 402c22052325349231090eb4cf4826159231fefe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679669"
---
# <a name="enable-on-demand-bursting"></a>Abilita espansione su richiesta

Unità SSD (Solid-State Drive) Premium hanno due modelli di picchi disponibili. espansione basata su crediti e picchi su richiesta. Questo articolo illustra come passare a una sequenza di espansione su richiesta. I dischi che usano il modello su richiesta possono superare le proprie destinazioni di provisioning originali. L'espansione su richiesta si verifica con la frequenza necessaria per il carico di lavoro, fino alla destinazione massima di espansione. Il picchi su richiesta comporta costi aggiuntivi.

Per informazioni dettagliate sull'espansione del disco, vedere la pagina relativa all'espansione del [disco gestito](disk-bursting.md).

> [!IMPORTANT]
> Non è necessario seguire la procedura descritta in questo articolo per usare l'espansione basata sul credito. Per impostazione predefinita, l'espansione basata su crediti è abilitata in tutti i dischi idonei.

Prima di abilitare l'espansione su richiesta, comprendere quanto segue:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Introduzione

È possibile abilitare l'espansione su richiesta con il modulo Azure PowerShell, l'interfaccia della riga di comando di Azure o i modelli Azure Resource Manager. Gli esempi seguenti illustrano come creare un nuovo disco con la funzionalità di espansione su richiesta abilitata e abilitando l'espansione su richiesta sui dischi esistenti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet di espansione su richiesta sono disponibili nella versione 5.5.0 e successive del modulo AZ. In alternativa, è possibile usare la [Azure cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Creare un disco dati vuoto con espansione su richiesta

Un disco gestito deve avere una dimensione maggiore di 512 GiB per abilitare l'espansione su richiesta. Sostituire i `<myResourceGroupDisk>` `<myDataDisk>` parametri e quindi eseguire lo script seguente per creare un'unità SSD Premium con una sequenza di espansione su richiesta:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Abilitare l'espansione su richiesta su un disco esistente

Un disco gestito deve avere una dimensione maggiore di 512 GiB per abilitare l'espansione su richiesta. Sostituire i `<myResourceGroupDisk>` `<myDataDisk>` parametri, ed eseguire questo comando per abilitare l'espansione su richiesta su un disco esistente:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet di espansione su richiesta sono disponibili nella versione 2.19.0 e successive del modulo dell' [interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)della riga di comando di Azure. In alternativa, è possibile usare la [Azure cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Creare e alleghi un disco dati di espansione su richiesta

Un disco gestito deve avere una dimensione maggiore di 512 GiB per abilitare l'espansione su richiesta. Sostituire i `<yourDiskName>` `<yourResourceGroup>` parametri, e `<yourVMName>` , quindi eseguire i comandi seguenti per creare un'unità SSD Premium con una sequenza di espansione su richiesta:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Abilitare l'espansione su richiesta su un disco esistente-interfaccia della riga di comando

Un disco gestito deve avere una dimensione maggiore di 512 GiB per abilitare l'espansione su richiesta. Sostituire i `<myResourceGroupDisk>` `<yourDiskName>` parametri e ed eseguire questo comando per abilitare l'espansione su richiesta su un disco esistente:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Con l' `2020-09-30` API del disco è possibile abilitare la creazione di picchi su richiesta su SSD Premium nuovi o esistenti di dimensioni maggiori di 512 GiB. L' `2020-09-30` API ha introdotto una nuova proprietà, `burstingEnabled` . Per impostazione predefinita, questa proprietà è impostata su false. Il modello di esempio seguente crea un'unità SSD 1TiB Premium negli Stati Uniti centro-occidentali con l'espansione del disco abilitata:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottenere informazioni sulle risorse di espansione, vedere metriche di espansione del [disco](disks-metrics.md).