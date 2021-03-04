---
title: Crittografare attestazioni di volume permanenti con una chiave gestita dal cliente (CMK) in Azure Red Hat OpenShift (ARO)
description: Istruzioni per la distribuzione di BYOK (Bring your own key)/CMK (Customer-Managed Key) per Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: Encryption, Byok, Aro, OpenShift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054781"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Crittografare attestazioni di volume permanenti con una chiave gestita dal cliente (CMK) in Azure Red Hat OpenShift (ARO) (anteprima)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite dalla piattaforma Microsoft che includono i dischi del sistema operativo e dei dati. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per crittografare i dati nei cluster OpenShift di Azure Red Hat.

> [!NOTE]
> In questa fase, il supporto esiste solo per la crittografia dei volumi di ARO persistenti con chiavi gestite dal cliente. Questa funzionalità non è attualmente disponibile per i dischi del sistema operativo.

> [!IMPORTANT]
> Le funzionalità di anteprima di ARO sono disponibili in base all'opzione self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime di ARO sono parzialmente coperte dal supporto tecnico del cliente in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione.

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone quanto segue:

* Si dispone di un cluster ARO preesistente in OpenShift versione 4,4 (o successiva).

* Si ha lo strumento da riga di comando OpenShift ' OC ', Base64 (parte di core utils) e l'interfaccia della riga di comando di Azure ' AZ ' installata.

* Si è connessi al cluster ARO usando *OC* come utente amministratore globale del cluster (kubeadmin).

* Si è connessi all'interfaccia della riga di comando di Azure usando *AZ* con un account autorizzato per concedere l'accesso "collaboratore" nella stessa sottoscrizione del cluster Aro.

## <a name="limitations"></a>Limitazioni

* La disponibilità per la crittografia della chiave gestita dal cliente è specifica dell'area. Per visualizzare lo stato di un'area di Azure specifica, controllare le [aree di Azure][supported-regions].
* Se si usano dischi Ultra, abilitare i dischi Ultra nella sottoscrizione prima di iniziare.

## <a name="create-an-azure-key-vault-instance"></a>Creare un'istanza di Azure Key Vault
Per archiviare le chiavi, è necessario usare un'istanza di Azure Key Vault. Creare una nuova Key Vault con la protezione ripulitura e l'eliminazione temporanea abilitata. Quindi, creare una nuova chiave nell'insieme di credenziali per archiviare la propria chiave personalizzata:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Creare un set di crittografia dischi di Azure

Il set di crittografia dischi di Azure viene usato come punto di riferimento per i dischi in ARO. È connessa al Azure Key Vault creato nel passaggio precedente e effettuerà il pull delle chiavi gestite dal cliente da tale posizione.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Concedere al set di crittografia del disco l'accesso Key Vault
Usare il set di crittografia del disco creato nei passaggi precedenti e concedere al set di crittografia del disco l'accesso a Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Ottenere altri ID richiesti per le assegnazioni di ruolo
È necessario consentire al cluster ARO di usare la crittografia del disco impostata per crittografare le attestazioni del volume permanente (PVC) nel cluster ARO. A tale scopo, verrà creato un nuovo identità del servizio gestita (MSI). Verranno inoltre impostate altre autorizzazioni per ARO MSI e per il set di crittografia del disco.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Implementare altre assegnazioni di ruolo necessarie per la crittografia BYOK/CMK
Applicare le assegnazioni di ruolo necessarie usando le variabili ottenute nel passaggio precedente:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Creare una classe di archiviazione K8S per la crittografia Premium & ultra Disks (facoltativo)
Generare le classi di archiviazione da usare per BYOK/CMK per i dischi Premium_LRS e UltraSSD_LRS:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>Configurare la configurazione della classe di archiviazione
Sostituire le variabili univoche del cluster ARO con i due file di configurazione della classe di archiviazione:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Eseguire quindi questa distribuzione nel cluster ARO per applicare la configurazione della classe di archiviazione:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Testare la crittografia con chiavi gestite dal cliente
Per verificare se il cluster usa una chiave gestita dal cliente per la crittografia del PVC, viene creata un'attestazione di volume permanente con la classe di archiviazione appropriata. Il frammento di codice seguente crea un pod e monta un'attestazione di volume permanente usando dischi standard
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Applicare il file di configurazione del test Pod
Eseguire i comandi seguenti per applicare la configurazione del pod di test e restituire l'UID della nuova attestazione del volume permanente. L'UID verrà usato per verificare che il disco sia crittografato con BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Verificare che il disco PVC sia configurato con "EncryptionAtRestWithCustomerKey" 
Il Pod deve creare un'attestazione di volume permanente che fa riferimento alla classe di archiviazione BYOK/CMK. Eseguendo il comando seguente, si convaliderà che il PVC è stato distribuito come previsto:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Passaggi successivi

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

