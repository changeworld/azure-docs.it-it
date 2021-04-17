---
title: Abilitare l'burst del disco su richiesta
description: Abilitare l'burst del disco su richiesta nel disco gestito.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483147"
---
# <a name="enable-on-demand-bursting"></a>Abilitare il bursting su richiesta

Le unità SSD (Solid-State Drive) Premium hanno due modelli di burst disponibili. bursting basato sul credito e burst su richiesta. Questo articolo illustra come passare al bursting su richiesta. I dischi che usano il modello su richiesta possono superare le destinazioni di cui è stato effettuato il provisioning originale. Il bursting su richiesta viene eseguito con la frequenza necessaria per il carico di lavoro, fino alla destinazione di burst massima. Il bursting su richiesta comporta costi aggiuntivi.

Per informazioni dettagliate sull'burst del disco, vedere [Bursting del disco gestito](disk-bursting.md).

> [!IMPORTANT]
> Non è necessario seguire la procedura descritta in questo articolo per usare il bursting basato sul credito. Per impostazione predefinita, lo burst basato sul credito è abilitato in tutti i dischi idonei.

Prima di abilitare il bursting su richiesta, comprendere quanto segue:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Introduzione

Lo bursting su richiesta può essere abilitato con il modulo Azure PowerShell, l'interfaccia della riga di comando di Azure o Azure Resource Manager modelli. Gli esempi seguenti illustrano come creare un nuovo disco con burst su richiesta abilitato e abilitare il bursting su richiesta nei dischi esistenti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet di burst su richiesta sono disponibili nella versione 5.5.0 e più recente del modulo Az. In alternativa, è possibile usare il [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Creare un disco dati vuoto con burst su richiesta

Un disco gestito deve essere maggiore di 512 GiB per abilitare il bursting su richiesta. Sostituire i `<myResourceGroupDisk>` parametri e e quindi eseguire lo script seguente per creare un `<myDataDisk>` SSD Premium con burst su richiesta:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Abilitare l'burst su richiesta in un disco esistente

Un disco gestito deve essere maggiore di 512 GiB per abilitare il bursting su richiesta. Sostituire i `<myResourceGroupDisk>` parametri , ed eseguire questo comando per abilitare `<myDataDisk>` l'burst su richiesta in un disco esistente:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet di burst su richiesta sono disponibili nella versione 2.19.0 e più recente del modulo dell'interfaccia della riga [di comando di Azure.](/cli/azure/install-azure-cli) In alternativa, è possibile usare il [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Creare e collegare un disco dati con burst su richiesta

Un disco gestito deve essere maggiore di 512 GiB per abilitare il bursting su richiesta. Sostituire i parametri , e , quindi eseguire i comandi seguenti per creare un `<yourDiskName>` `<yourResourceGroup>` `<yourVMName>` SSD Premium con burst su richiesta:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Abilitare l'burst su richiesta in un disco esistente - Interfaccia della riga di comando

Un disco gestito deve essere maggiore di 512 GiB per abilitare il bursting su richiesta. Sostituire i `<myResourceGroupDisk>` parametri e ed eseguire questo comando per abilitare `<yourDiskName>` l'burst su richiesta in un disco esistente:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Con l'API del disco, è possibile abilitare il bursting su richiesta su SSD Premium appena creati o esistenti maggiori `2020-09-30` di 512 GiB. `2020-09-30`L'API ha introdotto una nuova proprietà, `burstingEnabled` . Per impostazione predefinita, questa proprietà è impostata su false. Il modello di esempio seguente crea un disco SSD Premium 1TiB negli Stati Uniti centro-occidentali, con l'burst del disco abilitato:

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

Per informazioni su come ottenere informazioni dettagliate sulle risorse in burst, vedere [Metriche di burst del disco](disks-metrics.md).
