---
title: Abilitare la crittografia del disco per Service Fabric nodi del cluster gestito (anteprima)
description: Informazioni su come abilitare la crittografia dei dischi per i nodi del cluster gestito di Azure Service Fabric in Windows usando un modello ARM.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100642513"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Abilitare la crittografia del disco per Service Fabric nodi del cluster gestito (anteprima)

Questa guida illustra come abilitare la crittografia dei dischi in Service Fabric nodi del cluster gestito in Windows usando la funzionalità di [crittografia dischi di Azure](../virtual-machines/windows/disk-encryption-overview.md) per i [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) tramite i modelli di Azure Resource Manager (ARM).

> [!IMPORTANT]
> L'anteprima di crittografia dischi del set di scalabilità di macchine virtuali non supporta ancora l'aggiornamento dell'immagine o la ricreazione dell'immagine. Non usare se è necessario aggiornare l'immagine del sistema operativo.

## <a name="register-for-azure-disk-encryption"></a>Registrazione per crittografia dischi di Azure

L'anteprima della crittografia del disco per il set di scalabilità di macchine virtuali richiede la registrazione automatica. Eseguire il comando seguente:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Verificare lo stato della registrazione eseguendo:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Quando lo stato diventa *registrato*, si è pronti per continuare.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Provisioning di un Key Vault per la crittografia del disco

Crittografia dischi di Azure richiede Azure Key Vault per controllare e gestire segreti e chiavi di crittografia dei dischi. Il Key Vault e il cluster gestito da Service Fabric devono trovarsi nella stessa area e nella stessa sottoscrizione di Azure. Se questi requisiti vengono soddisfatti, è possibile usare una Key Vault nuova o esistente abilitando la crittografia del disco.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Creare Key Vault con la crittografia del disco abilitata

Eseguire i comandi seguenti per creare un nuovo Key Vault per la crittografia del disco. Assicurarsi che l'area per la Key Vault sia [supportata per i cluster gestiti Service Fabric](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) e che si trovi nella stessa area del cluster.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Aggiornare Key Vault esistenti per abilitare la crittografia del disco

Eseguire i comandi seguenti per abilitare la crittografia del disco per un Key Vault esistente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Aggiornare i file di modello e di parametri per la crittografia del disco

Il passaggio seguente illustra le modifiche necessarie ai modelli per abilitare la crittografia del disco in un [cluster gestito esistente](tutorial-managed-cluster-deploy.md). In alternativa, è possibile distribuire un nuovo cluster gestito con Service Fabric con la crittografia del disco abilitata con questo modello: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Aggiungere i parametri seguenti al modello, sostituendo la propria sottoscrizione, il nome del gruppo di risorse e il nome dell'insieme di credenziali in `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Successivamente, aggiungere l' `AzureDiskEncryption` estensione della macchina virtuale ai tipi di nodo del cluster gestito nel modello:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Aggiornare infine il file dei parametri, sostituendo la propria sottoscrizione, il gruppo di risorse e il nome dell'insieme di credenziali delle chiavi in *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Distribuire e verificare le modifiche

Quando si è pronti, distribuire le modifiche per abilitare la crittografia del disco nel cluster gestito.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

È possibile controllare lo stato della crittografia del disco nel set di scalabilità sottostante di un tipo di nodo usando il `Get-AzVmssDiskEncryption` comando. Prima di tutto è necessario trovare il nome del gruppo di risorse di supporto del cluster gestito (contenente la rete virtuale sottostante, il servizio di bilanciamento del carico, l'indirizzo IP pubblico, NSG, i set di scalabilità e gli account di archiviazione). Assicurarsi di modificare `VmssName` il nome del tipo di nodo del cluster che si vuole controllare (come specificato nel modello di distribuzione).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

L'output dovrebbe essere simile al seguente:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Passaggi successivi

[Esempio: SKU Standard Service Fabric cluster gestito, un tipo di nodo con crittografia del disco abilitato](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Crittografia dischi di Azure per macchine virtuali Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Crittografare i set di scalabilità di macchine virtuali con Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
