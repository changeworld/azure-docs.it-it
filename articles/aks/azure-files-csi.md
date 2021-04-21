---
title: Usare Container Storage Interface (CSI) per File di Azure in servizio Azure Kubernetes (AKS)
description: Informazioni su come usare i driver Container Storage Interface (CSI) per File di Azure in un cluster servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: a83d2222862db6bc3e3ff86ba4074114c1a872e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776160"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usare File di Azure Container Storage Interface (CSI) in servizio Azure Kubernetes (AKS) (anteprima)

Il driver File di Azure Container Storage Interface (CSI) è un driver conforme alle specifiche [CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)usato da servizio Azure Kubernetes (AKS) per gestire il ciclo di vita File di Azure condivisioni.

CSI è uno standard per esporre i sistemi di archiviazione di file e blocchi arbitrari a carichi di lavoro in contenitori in Kubernetes. Adottando e usando CSI, il servizio Azure Kubernetes ora può scrivere, distribuire ed eseguire l'iterazione dei plug-in per esporre nuovi o migliorare i sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Per creare un cluster del servizio AzureKs con il supporto del driver CSI, vedere Abilitare i driver CSI per dischi di Azure e File di Azure [nel servizio AzureKs.](csi-storage-drivers.md)

>[!NOTE]
> *I driver in-tree* si riferiscono ai driver di archiviazione correnti che fanno parte del codice Kubernetes principale rispetto ai nuovi driver CSI, che sono plug-in.

## <a name="use-a-persistent-volume-with-azure-files"></a>Usare un volume permanente con File di Azure

Un [volume permanente (PV)](concepts-storage.md#persistent-volumes) rappresenta una parte di archiviazione di cui è stato effettuato il provisioning per l'uso con pod Kubernetes. Un PV può essere usato da uno o più pod e può essere effettuato in modo dinamico o statico. Se più pod necessitano di accesso simultaneo allo stesso volume di archiviazione, è possibile usare File di Azure per connettersi usando il [protocollo Server Message Block (SMB).][smb-overview] Questo articolo illustra come creare dinamicamente una condivisione File di Azure per l'uso da parte di più pod in un cluster del servizio Web Dick. Per il provisioning statico, vedere Creare e usare manualmente [un volume con una File di Azure condivisione](azure-files-volume.md).

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Creare dinamicamente File di Azure PV usando le classi di archiviazione incorporate

Una classe di archiviazione viene usata per definire la modalità File di Azure una condivisione di archiviazione. Nel gruppo di risorse [][node-resource-group] del nodo viene creato automaticamente un account di archiviazione da usare con la classe di archiviazione per contenere le File di Azure di archiviazione. Scegliere uno degli SKU di [ridondanza di Archiviazione di Azure seguenti][storage-skus] per *skuName:*

* **Standard_LRS:** Archiviazione con ridondanza locale Standard
* **Standard_GRS:** Archiviazione con ridondanza geografica standard
* **Standard_ZRS:** Archiviazione con ridondanza della zona Standard
* **Standard_RAGRS:** Archiviazione con ridondanza geografica e accesso in lettura Standard
* **Premium_LRS:** Archiviazione con ridondanza locale Premium

> [!NOTE]
> File di Azure supporta Azure Archiviazione Premium. La condivisione file Premium minima è 100 GB.

Quando si usano i driver CSI di archiviazione nel servizio AzureKs, sono disponibili due altri driver predefiniti che usano File di Azure `StorageClasses` di archiviazione CSI. Le classi di archiviazione CSI aggiuntive vengono create con il cluster insieme alle classi di archiviazione predefinite nell'albero.

- `azurefile-csi`: usa Archiviazione Standard di Azure per creare una condivisione File di Azure distribuzione.
- `azurefile-csi-premium`: usa Azure Archiviazione Premium per creare una condivisione File di Azure distribuzione.

Il criterio di recupero in entrambe le classi di archiviazione garantisce che la condivisione File di Azure sottostante venga eliminata quando viene eliminato il rispettivo PV. Le classi di archiviazione configurano anche le condivisioni file in modo che siano espandibili. È sufficiente modificare l'attestazione di volume permanente (PVC) con le nuove dimensioni.

Per usare queste classi di archiviazione, creare un [PVC](concepts-storage.md#persistent-volume-claims) e il rispettivo pod che le faccia riferimento e le usi. Un PVC viene usato per effettuare automaticamente il provisioning dell'archiviazione in base a una classe di archiviazione. Un PVC può usare una delle classi di archiviazione create in precedenza o una classe di archiviazione definita dall'utente per creare una condivisione File di Azure per lo SKU e le dimensioni desiderate. Quando si crea una definizione di pod, viene specificato il PVC per richiedere l'archiviazione desiderata.

Creare un [pod e un PVC `outfile` ](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) di esempio che stampa la data corrente in un oggetto con il [comando kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Quando il pod è in esecuzione, è possibile verificare che la condivisione file sia montata correttamente eseguendo il comando seguente e verificando che l'output contenga `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Creare una classe di archiviazione personalizzata

Le classi di archiviazione predefinite sono adatte per gli scenari più comuni, ma non per tutte. In alcuni casi, potrebbe essere necessario personalizzare la propria classe di archiviazione con parametri personalizzati. Ad esempio, usare il manifesto seguente per configurare `mountOptions` della condivisione file.

Il valore predefinito per *fileMode* e *dirMode* è *0777 per* le condivisioni file montate di Kubernetes. È possibile specificare le diverse opzioni di montaggio nell'oggetto classe di archiviazione.

Creare un file denominato `azure-file-sc.yaml` e incollare il manifesto di esempio seguente:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Creare la classe di archiviazione con il comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Il File di Azure driver CSI supporta la creazione [di snapshot di volumi persistenti](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) e condivisioni file sottostanti.

Creare una [classe di snapshot del](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) volume con il comando [kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Creare uno [snapshot del volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) dal PVC creato dinamicamente [all'inizio di questa esercitazione,](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Verificare che lo snapshot sia stato creato correttamente:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Ridimensionare un volume permanente

È possibile richiedere un volume più grande per un PVC. Modificare l'oggetto PVC e specificare dimensioni maggiori. Questa modifica attiva l'espansione del volume sottostante che contiene il valore PV.

> [!NOTE]
> Un nuovo PV non viene mai creato per soddisfare l'attestazione. Viene invece ridimensionato un volume esistente.

Nel servizio AzureKs la classe di archiviazione predefinita supporta già l'espansione, quindi usare il PVC creato in precedenza `azurefile-csi` con questa classe di [archiviazione](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). Il PVC ha richiesto una condivisione file da 100Gi. È possibile verificarlo eseguendo:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Espandere il PVC aumentando il `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Verificare che sia il PVC che il file system all'interno del pod mostrino le nuove dimensioni:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>Condivisioni file NFS
[File di Azure ora è disponibile il supporto per il protocollo NFS v4.1.](../storage/files/storage-files-how-to-create-nfs-shares.md) Il supporto NFS 4.1 per File di Azure offre un file system NFS completamente gestito come servizio basato su una piattaforma di archiviazione resiliente distribuita a disponibilità elevata e altamente durevole.

 Questa opzione è ottimizzata per carichi di lavoro ad accesso casuale con aggiornamenti dei dati sul posto e offre il supporto completo di file system POSIX. Questa sezione illustra come usare condivisioni NFS con il driver CSI di File di Azure in un cluster del servizio AzureKs.

Assicurarsi di controllare le limitazioni [e la disponibilità](../storage/files/storage-files-compare-protocols.md#limitations) [dell'area durante](../storage/files/storage-files-compare-protocols.md#regional-availability) la fase di anteprima.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrare la `AllowNfsFileShares` funzionalità di anteprima

Per creare una condivisione file che sfrutta NFS 4.1, è necessario abilitare il `AllowNfsFileShares` flag di funzionalità nella sottoscrizione.

Registrare `AllowNfsFileShares` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando [il comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider *di risorse Microsoft.Storage* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Creare un account di archiviazione per la condivisione file NFS

[Creare un oggetto `Premium_LRS` Account di archiviazione di Azure](../storage/files/storage-how-to-create-file-share.md) con le configurazioni seguenti per supportare le condivisioni NFS:
- tipo di account: FileStorage
- trasferimento sicuro obbligatorio (abilitare solo il traffico HTTPS): false
- Selezionare la rete virtuale dei nodi agente in Firewall e reti virtuali, quindi è preferibile creare l'account di archiviazione nel gruppo MC_ risorse.

### <a name="create-nfs-file-share-storage-class"></a>Creare una classe di archiviazione di condivisione file NFS

Salvare un `nfs-sc.yaml` file con il manifesto seguente modificando i rispettivi segnaposto.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Dopo aver modificato e salvato il file, creare la classe di archiviazione con il [comando kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Creare una distribuzione con una condivisione file supportata da NFS
È possibile distribuire un [set con](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) stato di esempio che salva i timestamp in un file distribuendo il comando seguente con il `data.txt` comando [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Convalidare il contenuto del volume eseguendo:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Si noti che poiché la condivisione file NFS si trova nell'account Premium, le dimensioni minime della condivisione file sono di 100 GB. Se si crea un PVC con dimensioni di archiviazione ridotte, potrebbe verificarsi un errore "Impossibile creare la condivisione file... size (5)...".


## <a name="windows-containers"></a>Contenitori Windows

Il File di Azure driver CSI supporta anche i nodi e i contenitori di Windows. Se si vogliono usare contenitori windows, seguire [l'esercitazione sui contenitori di Windows](windows-container-cli.md) per aggiungere un pool di nodi windows.

Dopo aver creato un pool di nodi di Windows, usare le classi di archiviazione incorporate, ad esempio `azurefile-csi` o crearne di personalizzate. È possibile distribuire un set con stato basato su [Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) di esempio che salva i timestamp in un file distribuendo il comando seguente con il `data.txt` comando [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Convalidare il contenuto del volume eseguendo:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare i driver CSI per i dischi di Azure, vedere [Usare dischi di Azure con i driver CSI.](azure-disk-csi.md)
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere Procedure consigliate per l'archiviazione e [i backup in servizio Azure Kubernetes][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md