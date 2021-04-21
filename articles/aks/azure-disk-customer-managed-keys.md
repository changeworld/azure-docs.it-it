---
title: Usare una chiave gestita dal cliente per crittografare i dischi di Azure in servizio Azure Kubernetes (AKS)
description: Bring Your Own Key (BYOK) per crittografare i dischi dati e del sistema operativo del servizio Web Del servizio Web Diaks.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776192"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Bring Your Own Key (BYOK) con dischi di Azure servizio Azure Kubernetes (AKS)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un controllo aggiuntivo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati in pausa per i dischi del sistema operativo e dei dati per i cluster del servizio Web Diaks. Altre informazioni sulle chiavi gestite dal cliente in [Linux][customer-managed-keys-linux] e [Windows.][customer-managed-keys-windows]

## <a name="limitations"></a>Limitazioni
* Il supporto della crittografia del disco dati è limitato ai cluster del servizio Kubernetes che eseguono Kubernetes versione 1.17 e successive.
* La crittografia del sistema operativo e del disco dati con chiavi gestite dal cliente può essere abilitata solo durante la creazione di un cluster del servizio Web Diaks.

## <a name="prerequisites"></a>Prerequisiti
* È necessario abilitare l'eliminazione e la protezione dall'eliminazione *Azure Key Vault* quando si usa Key Vault per crittografare i dischi gestiti.
* È necessaria l'interfaccia della riga di comando di Azure versione 2.11.1 o successiva.

## <a name="create-an-azure-key-vault-instance"></a>Creare un'istanza Azure Key Vault servizio

Usare un'Azure Key Vault per archiviare le chiavi.  Facoltativamente, è possibile usare il portale di Azure per [configurare le chiavi gestite dal cliente con Azure Key Vault][byok-azure-portal]

Creare un nuovo gruppo *di risorse,* quindi creare una nuova *istanza Key Vault* e abilitare l'eliminazione e la protezione dall'eliminazione.  Assicurarsi di usare gli stessi nomi di area e gruppo di risorse per ogni comando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Creare un'istanza di diskEncryptionSet

Sostituire *myKeyVaultName con* il nome dell'insieme di credenziali delle chiavi.  Sarà anche necessaria una *chiave archiviata* in Azure Key Vault per completare i passaggi seguenti.  Archiviare la chiave esistente nel Key Vault creato nei passaggi [][key-vault-generate] precedenti oppure generare una nuova chiave e sostituire *myKeyName* di seguito con il nome della chiave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Concedere a DiskEncryptionSet l'accesso all'insieme di credenziali delle chiavi

Usare DiskEncryptionSet e i gruppi di risorse creati nei passaggi precedenti e concedere alla risorsa DiskEncryptionSet l'accesso al Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Creare un nuovo cluster del servizio AKS e crittografare il disco del sistema operativo

Creare un **nuovo gruppo di risorse e** un cluster del servizio Web Del servizio Web, quindi usare la chiave per crittografare il disco del sistema operativo. Le chiavi gestite dal cliente sono supportate solo nelle versioni di Kubernetes superiori alla 1.17. 

> [!IMPORTANT]
> Assicurarsi di creare un nuovo gruppo di disponibilità per il cluster del servizio Web Disatteso

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Quando vengono aggiunti nuovi pool di nodi al cluster creato in precedenza, la chiave gestita dal cliente fornita durante la creazione viene usata per crittografare il disco del sistema operativo.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Crittografare il disco dati del cluster del servizio Servizio Web Di rete (facoltativo)
La chiave di crittografia del disco del sistema operativo verrà usata per crittografare il disco dati se la chiave non viene fornita per il disco dati dalla versione 1.17.2 ed è anche possibile crittografare i dischi dati del servizio Web Disack con le altre chiavi.

> [!IMPORTANT]
> Assicurarsi di disporre delle credenziali appropriate del servizio Web Disattesa. L'identità gestita dovrà avere accesso come collaboratore al gruppo di risorse in cui viene distribuito diskencryptionset. In caso contrario, verrà visualizzato un errore che indica che l'identità gestita non dispone di autorizzazioni.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Creare un file denominato **byok-azure-disk.yaml** contenente le informazioni seguenti.  Sostituire myAzureSubscriptionId, myResourceGroup e myDiskEncrptionSetName con i valori e applicare yaml.  Assicurarsi di usare il gruppo di risorse in cui è distribuito DiskEncryptionSet.  Se si usa il Azure Cloud Shell, questo file può essere creato usando vi o nano come se si lavora su un sistema virtuale o fisico:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Eseguire quindi questa distribuzione nel cluster del servizio AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le procedure consigliate per la sicurezza del [cluster del servizio Web Diaks][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions