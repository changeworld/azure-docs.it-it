---
title: Abilitare la crittografia end-to-end usando la crittografia nell'host - Interfaccia della riga di comando di Azure - Dischi gestiti
description: Usare la crittografia nell'host per abilitare la crittografia end-to-end nei dischi gestiti di Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533605"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Usare l'interfaccia della riga di comando di Azure per abilitare la crittografia end-to-end usando la crittografia nell'host

Quando si abilita la crittografia nell'host, i dati archiviati nell'host della macchina virtuale vengono crittografati quando sono in pausa e vengono crittografati nel servizio di archiviazione. Per informazioni concettuali sulla crittografia nell'host e su altri tipi di crittografia dei dischi gestiti, vedere [Crittografia nell'host - Crittografia end-to-end](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)per i dati della macchina virtuale.

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

L'elenco completo delle dimensioni di VM supportate può essere estratto a livello di codice. Per informazioni su come recuperarli a livello di codice, vedere la sezione Ricerca [delle dimensioni delle macchine virtuali](#finding-supported-vm-sizes) supportate.
L'aggiornamento delle dimensioni della macchina virtuale comporta la convalida per verificare se le nuove dimensioni della macchina virtuale supportano la funzionalità EncryptionAtHost.

## <a name="prerequisites"></a>Prerequisiti

È necessario abilitare la funzionalità per la sottoscrizione prima di usare la proprietà EncryptionAtHost per la macchina virtuale o il vmSS. Seguire questa procedura per abilitare la funzionalità per la sottoscrizione:

1.  Eseguire il comando seguente per registrare la funzionalità per la sottoscrizione

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Verificare che lo stato della registrazione sia Registrato (richiede alcuni minuti) usando il comando seguente prima di provare la funzionalità.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Creare un Azure Key Vault DiskEncryptionSet

Dopo aver abilitato la funzionalità, è necessario configurare un Azure Key Vault e un diskEncryptionSet, se non è già stato fatto.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Esempio

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Creare una macchina virtuale con la crittografia nell'host abilitata con chiavi gestite dal cliente. 

Creare una macchina virtuale con dischi gestiti usando l'URI della risorsa diskEncryptionSet creato in precedenza per crittografare la cache dei dischi del sistema operativo e dei dati con chiavi gestite dal cliente. I dischi temporanei vengono crittografati con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Creare una macchina virtuale con la crittografia nell'host abilitata con chiavi gestite dalla piattaforma. 

Creare una macchina virtuale con crittografia nell'host abilitata per crittografare la cache dei dischi del sistema operativo/dati e dei dischi temporanei con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Aggiornare una macchina virtuale per abilitare la crittografia nell'host. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Controllare lo stato della crittografia nell'host per una macchina virtuale

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Creare un set di scalabilità di macchine virtuali con la crittografia nell'host abilitata con chiavi gestite dal cliente. 

Creare un set di scalabilità di macchine virtuali con dischi gestiti usando l'URI della risorsa diskEncryptionSet creato in precedenza per crittografare la cache dei dischi dati e del sistema operativo con chiavi gestite dal cliente. I dischi temporanei vengono crittografati con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Creare un set di scalabilità di macchine virtuali con la crittografia nell'host abilitata con chiavi gestite dalla piattaforma. 

Creare un set di scalabilità di macchine virtuali con la crittografia nell'host abilitata per crittografare la cache dei dischi del sistema operativo/dati e dei dischi temporanei con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Aggiornare un set di scalabilità di macchine virtuali per abilitare la crittografia nell'host. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Controllare lo stato della crittografia nell'host per un set di scalabilità di macchine virtuali

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Ricerca delle dimensioni delle macchine virtuali supportate

Le dimensioni delle macchine virtuali legacy non sono supportate. È possibile trovare l'elenco delle dimensioni delle macchine virtuali supportate:

Chiamata [dell'API SKU della risorsa](/rest/api/compute/resourceskus/list) e verifica che `EncryptionAtHostSupported` la funzionalità sia impostata su **True.**

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

In caso contrario, chiamare il cmdlet [Di PowerShell Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku)

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato e configurato queste risorse, è possibile usarle per proteggere i dischi gestiti. Il collegamento seguente contiene script di esempio, ognuno con un rispettivo scenario, che è possibile usare per proteggere i dischi gestiti.

[Esempi di modelli di Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
