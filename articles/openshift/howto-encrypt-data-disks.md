---
title: Crittografare le attestazioni di volume persistenti con una chiave gestita dal cliente (CMK) Azure Red Hat OpenShift (ARO)
description: Istruzioni di distribuzione bring your own key (BYOK) /CMK (Customer-managed key) per Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: crittografia, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783522"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Crittografare le attestazioni di volume persistenti con una chiave gestita dal cliente (CMK) in Azure Red Hat OpenShift (anteprima)

Archiviazione di Azure usa la crittografia lato server (SSE) per crittografare automaticamente i dati quando vengono resi persistenti nel cloud. [](../storage/common/storage-service-encryption.md) Per impostazione predefinita, i dati vengono crittografati con chiavi gestite dalla piattaforma Microsoft. Per un ulteriore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per crittografare i dati nei cluster Azure Red Hat OpenShift personalizzati.

> [!NOTE]
> In questa fase, il supporto è disponibile solo per crittografare i volumi permanenti ARO con chiavi gestite dal cliente. Questa funzionalità non è attualmente disponibile per i dischi del sistema operativo del nodo master o del nodo di lavoro.

> [!IMPORTANT]
> Le funzionalità di anteprima di ARO sono disponibili in modalità self-service e con consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime di ARO sono parzialmente coperte dal supporto clienti in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione.

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone quanto segue:

* È disponibile un cluster ARO preesistnte in OpenShift versione 4.4 (o successiva).

* Sono installati lo strumento da riga di comando **oc** OpenShift, base64 (parte di coreutils) e l'interfaccia della riga di comando di Azure **az.**

* Si è connessi al cluster ARO usando **oc** come utente amministratore del cluster globale (kubeadmin).

* Si è connessi all'interfaccia della riga di comando di Azure usando **az** con un account autorizzato a concedere l'accesso "Collaboratore" nella stessa sottoscrizione del cluster ARO.

## <a name="limitations"></a>Limitazioni

* La disponibilità per la crittografia della chiave gestita dal cliente è specifica dell'area. Per visualizzare lo stato di un'area di Azure specifica, vedere Aree [di Azure.][supported-regions]
* Se si vuole usare Dischi Ultra, è necessario abilitarli nella sottoscrizione prima di iniziare.

## <a name="declare-cluster--encryption-variables"></a>Dichiarare variabili di crittografia & cluster
È necessario configurare le variabili seguenti in base a quanto potrebbe essere appropriato per il cluster ARO in cui si desidera abilitare le chiavi di crittografia gestite dal cliente:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Ottenere l'ID sottoscrizione
L'ID sottoscrizione di Azure viene usato più volte nella configurazione di CMK. Ottenerlo e archiviarlo come variabile:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Creare un'Azure Key Vault predefinita
Per archiviare le chiavi, è necessario usare Azure Key Vault'istanza di . Creare un nuovo Key Vault con la protezione di ripulitura abilitata. Creare quindi una nuova chiave all'interno dell'insieme di credenziali per archiviare la propria chiave personalizzata:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Creare un set di crittografia dischi di Azure

Il Crittografia dischi di Azure set viene usato come punto di riferimento per i dischi in ARO. È connesso al Azure Key Vault creato nel passaggio precedente e estrarrà le chiavi gestite dal cliente da tale posizione.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Concedere al set di crittografia del disco l'accesso Key Vault
Usare il set di crittografia del disco creato nei passaggi precedenti e concedere al set di crittografia del disco l'accesso Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Ottenere altri ID necessari per le assegnazioni di ruolo
È necessario consentire al cluster ARO di usare il set di crittografia del disco per crittografare le attestazioni di volume persistente (PVC) nel cluster ARO. A tale scopo, verrà creata una nuova identità del servizio gestito. Verranno impostate anche altre autorizzazioni per l'msi ARO e per il set di crittografia del disco.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementare altre assegnazioni di ruolo necessarie per la crittografia CMK
Applicare le assegnazioni di ruolo necessarie usando le variabili ottenute nel passaggio precedente:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Creare una classe di archiviazione k8s per dischi Premium & Ultra crittografati
Generare classi di archiviazione da usare per la CMK Premium_LRS e UltraSSD_LRS dischi:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Eseguire quindi questa distribuzione nel cluster ARO per applicare la configurazione della classe di archiviazione:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testare la crittografia con chiavi gestite dal cliente (facoltativo)
Per verificare se il cluster usa una chiave gestita dal cliente per la crittografia PVC, verrà creata un'attestazione di volume permanente usando la nuova classe di archiviazione. Il frammento di codice seguente crea un pod e monta un'attestazione di volume permanente usando dischi Premium.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
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
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Applicare il file di configurazione del pod di test (facoltativo)
Eseguire i comandi seguenti per applicare la configurazione del pod di test e restituire l'UID della nuova attestazione di volume permanente. L'UID verrà usato per verificare che il disco sia crittografato tramite CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> In alcuni casi può verificarsi un leggero ritardo durante l'applicazione delle assegnazioni di ruolo all'interno Azure Active Directory. A seconda della velocità di esecuzione di queste istruzioni, il comando "Determinare il nome completo del disco di Azure" potrebbe non riuscire. In questo caso, esaminare l'output di **oc describe pvc mypod-with-cmk-encryption-pvc** per assicurarsi che il provisioning del disco sia stato eseguito correttamente. Se la propagazione dell'assegnazione di  ruolo non è stata completata, potrebbe essere necessario eliminare e applicare nuovamente il pod & YAML PVC.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Verificare che il disco PVC sia configurato con "EncryptionAtRestWithCustomerKey" (facoltativo)
Il pod deve creare un'attestazione di volume permanente che fa riferimento alla classe di archiviazione CMK. L'esecuzione del comando seguente consente di verificare che il PVC sia stato distribuito come previsto:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions