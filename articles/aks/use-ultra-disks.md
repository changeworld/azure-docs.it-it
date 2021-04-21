---
title: Abilitare disco Ultra supporto per le servizio Azure Kubernetes (AKS)
description: Informazioni su come abilitare e configurare le Dischi Ultra in un cluster servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 7dbe0a75ce2079bdec752f7fee0c3e97e3ae2ffa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767350"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Usare dischi Ultra di Azure servizio Azure Kubernetes (anteprima)

[I dischi Ultra di Azure](../virtual-machines/disks-enable-ultra-ssd.md) offrono velocità effettiva elevata, operazioni di I/O al secondo elevate e archiviazione su disco coerente a bassa latenza per le applicazioni con stato. Uno dei principali vantaggi dei dischi Ultra è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza dover riavviare i nodi agente. I dischi Ultra sono adatti per carichi di lavoro a elevato utilizzo di dati.

## <a name="before-you-begin"></a>Prima di iniziare

Questa funzionalità può essere impostata solo al momento della creazione del cluster o del pool di nodi.

> [!IMPORTANT]
> I dischi Ultra di Azure richiedono pool di nodi distribuiti in aree e zone di disponibilità che supportano questi dischi, nonché solo serie di macchine virtuali specifiche. Vedere [**l'ambito e le limitazioni della ga ga dei dischi Ultra.**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)

### <a name="register-the-enableultrassd-preview-feature"></a>Registrare la `EnableUltraSSD` funzionalità di anteprima

Per creare un cluster del servizio AzureKs o un pool di nodi in grado di sfruttare i dischi Ultra, è necessario abilitare il `EnableUltraSSD` flag di funzionalità nella sottoscrizione.

Registrare `EnableUltraSSD` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster o un pool di nodi del servizio Dischi Ultra, è necessaria l'estensione dell'interfaccia della riga di comando *aks-preview* più recente. Installare *l'estensione dell'interfaccia* della riga di comando di Azure aks-preview usando il [comando az extension add][az-extension-add] oppure installare gli aggiornamenti disponibili usando il comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Limitazioni
- Vedere [ **l'ambito e le limitazioni della ga ga dei dischi Ultra**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- L'intervallo di dimensioni supportato per i dischi Ultra è compreso tra 100 e 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Creare un nuovo cluster che può usare dischi Ultra

Creare un cluster del servizio Web Diaks in grado di sfruttare Dischi Ultra usando i comandi dell'interfaccia della riga di comando seguenti. Usare il `--aks-custom-headers` flag per impostare la `EnableUltraSSD` funzionalità.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Creare il cluster servizio AKS con supporto per Dischi Ultra.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Se si vogliono creare cluster senza supporto di dischi Ultra, è possibile farlo omettendo il parametro `--aks-custom-headers` personalizzato.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Abilitare dischi Ultra in un cluster esistente

È possibile abilitare dischi Ultra nei cluster esistenti aggiungendo un nuovo pool di nodi al cluster che supporta i dischi Ultra. Configurare un nuovo pool di nodi per l'uso di dischi Ultra usando il `--aks-custom-headers` flag .

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Se si desidera creare nuovi pool di nodi senza supporto per i dischi Ultra, è possibile omettere il parametro `--aks-custom-headers` personalizzato.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Usare dischi Ultra in modo dinamico con una classe di archiviazione

Per usare dischi Ultra nelle distribuzioni o nei set con stato, è possibile usare una classe [di archiviazione per il provisioning dinamico.](azure-disks-dynamic-pv.md)

### <a name="create-the-storage-class"></a>Creare la classe di archiviazione

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

In questo caso, verrà creata una classe di archiviazione che fa riferimento ai dischi Ultra. Creare un file denominato `azure-ultra-disk-sc.yaml` e copiarlo nel manifesto seguente.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Creare la classe di archiviazione con il [comando kubectl apply][kubectl-apply] e specificare il file *azure-ultra-disk-sc.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. In questo caso, un PVC può usare la classe di archiviazione creata in precedenza per creare un disco Ultra.

Creare un file denominato `azure-ultra-disk-pvc.yaml` e copiarlo nel manifesto seguente. L'attestazione richiede un disco `ultra-disk` denominato con dimensioni di *1000 GB* con accesso *ReadWriteOnce.* La *classe di archiviazione ultra-disk-sc* viene specificata come classe di archiviazione.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Creare l'attestazione del volume persistente con il [comando kubectl apply][kubectl-apply] e specificare il file *azure-ultra-disk-pvc.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Usare il volume permanente

Dopo aver creato l'attestazione di volume persistente e aver effettuato il provisioning del disco, è possibile creare un pod con accesso al disco. Il manifesto seguente crea un pod NGINX di base che usa l'attestazione di volume permanente denominata *ultra-disk* per montare il disco di Azure nel percorso `/mnt/azure` .

Creare un file denominato `nginx-ultra.yaml` e copiarlo nel manifesto seguente.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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
        claimName: ultra-disk
```

Creare il pod con il comando [kubectl apply][kubectl-apply], come illustrato nell'esempio seguente:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

A questo punto è disponibile un pod in esecuzione con il disco di Azure montato nella directory `/mnt/azure`. Questa configurazione può essere visualizzata quando si controlla il pod tramite `kubectl describe pod nginx-ultra`, come illustrato nell'esempio sintetico seguente:

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui dischi Ultra, vedere [Uso dei dischi Ultra di Azure](../virtual-machines/disks-enable-ultra-ssd.md).
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere [Best practices for storage and backups in servizio Azure Kubernetes (AKS)][operator-best-practices-storage]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
