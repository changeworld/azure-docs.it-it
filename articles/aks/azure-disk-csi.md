---
title: Usare Container Storage Interface (CSI) per Dischi di Azure in servizio Azure Kubernetes (AKS)
description: Informazioni su come usare i driver Container Storage Interface (CSI) per i dischi di Azure in un cluster servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c3421b767f465a4a705bdeb4882fd261c5cf914f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776232"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usare i driver di azure disk Container Storage Interface (CSI) in servizio Azure Kubernetes (AKS) (anteprima)
Il driver CSI (Disk Container Storage Interface) di Azure è un driver conforme alle specifiche [CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)usato da servizio Azure Kubernetes (AKS) per gestire il ciclo di vita dei dischi di Azure.

CSI è uno standard per esporre i sistemi di archiviazione di file e blocchi arbitrari a carichi di lavoro in contenitori in Kubernetes. Adottando e usando CSI, il servizio Azure Kubernetes può scrivere, distribuire ed eseguire l'iterazione dei plug-in per esporre nuovi o migliorare i sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Per creare un cluster del servizio AzureKs con il supporto dei driver CSI, vedere Abilitare i driver CSI per dischi di Azure e File di Azure [nel servizio AzureKs.](csi-storage-drivers.md)

>[!NOTE]
> *I driver in-tree* fanno riferimento ai driver di archiviazione correnti che fanno parte del codice Kubernetes principale rispetto ai nuovi driver CSI, che sono plug-in.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Usare volumi permanenti CSI con i dischi di Azure

Un [volume permanente](concepts-storage.md#persistent-volumes) (PV) rappresenta una parte di archiviazione di cui è stato effettuato il provisioning per l'uso con pod Kubernetes. Un PV può essere usato da uno o più pod e può essere effettuato in modo dinamico o statico. Questo articolo illustra come creare dinamicamente IPV con dischi di Azure per l'uso da parte di un singolo pod in un cluster del servizio AzureKs. Per il provisioning statico, vedere [Creare e usare manualmente un volume con dischi di Azure.](azure-disk-volume.md)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Creare dinamicamente IPV su disco di Azure usando le classi di archiviazione incorporate

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere Classi di archiviazione [Kubernetes][kubernetes-storage-classes]. Quando si usano i driver CSI di archiviazione nel servizio AzureKs, sono disponibili due altri driver predefiniti che usano i driver di archiviazione `StorageClasses` CSI su disco di Azure. Le classi di archiviazione CSI aggiuntive vengono create con il cluster insieme alle classi di archiviazione predefinite nell'albero.

- `managed-csi`: usa Azure SSD Standard archiviazione con ridondanza locale per creare un disco gestito.
- `managed-csi-premium`: usa l'archiviazione con archiviazione con accesso in locale Premium di Azure per creare un disco gestito.

Il criterio di recupero in entrambe le classi di archiviazione garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il rispettivo PV. Le classi di archiviazione configurano anche i CSV in modo che siano espandibili. È sufficiente modificare l'attestazione di volume permanente (PVC) con le nuove dimensioni.

Per sfruttare queste classi di archiviazione, creare un [PVC](concepts-storage.md#persistent-volume-claims) e il rispettivo pod che le faccia riferimento e le usi. Un PVC viene usato per effettuare automaticamente il provisioning dell'archiviazione in base a una classe di archiviazione. Un PVC può usare una delle classi di archiviazione create in precedenza o una classe di archiviazione definita dall'utente per creare un disco gestito da Azure per lo SKU e le dimensioni desiderate. Quando si crea una definizione di pod, viene specificato il PVC per richiedere l'archiviazione desiderata.

Creare un pod di esempio e il rispettivo PVC con [il comando kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Quando il pod è in esecuzione, creare un nuovo file denominato `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

È ora possibile verificare che il disco sia montato correttamente eseguendo il comando seguente e verificando che il `test.txt` file venga visualizzato nell'output:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Creare una classe di archiviazione personalizzata

Le classi di archiviazione predefinite sono adatte per gli scenari più comuni, ma non per tutti. In alcuni casi, potrebbe essere necessario personalizzare la propria classe di archiviazione con i propri parametri. Si supponga, ad esempio, di voler modificare la `volumeBindingMode` classe .

È possibile usare una `volumeBindingMode: Immediate` classe che garantisca che si verifichi immediatamente dopo la creazione del PVC. Nei casi in cui i pool di nodi sono vincolati dalla topologia, ad esempio usando le zone di disponibilità, i pod vengono associati o di cui viene effettuato il provisioning senza conoscere i requisiti di pianificazione del pod (in questo caso in una zona specifica).

Per risolvere questo scenario, è possibile usare , che ritarda l'associazione e il provisioning di un PV fino a quando non viene creato un pod che `volumeBindingMode: WaitForFirstConsumer` usa il PVC. In questo modo, il PV sarà conforme e ne verrà effettuato il provisioning nella zona di disponibilità (o in un'altra topologia) specificata dai vincoli di pianificazione del pod. Le classi di archiviazione predefinite usano `volumeBindingMode: WaitForFirstConsumer` la classe .

Creare un file denominato `sc-azuredisk-csi-waitforfirstconsumer.yaml` e incollare il manifesto seguente.
La classe di archiviazione è la stessa della classe `managed-csi` di archiviazione, ma con una classe `volumeBindingMode` diversa.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Creare la classe di archiviazione con [il comando kubectl apply][kubectl-apply] e specificare il `sc-azuredisk-csi-waitforfirstconsumer.yaml` file:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Snapshot del volume

Il driver CSI del disco di Azure supporta la [creazione di snapshot di volumi persistenti.](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) Come parte di questa funzionalità, il  driver può eseguire snapshot completi o [ *incrementali*](../virtual-machines/disks-incremental-snapshots.md) a seconda del valore impostato nel parametro (per impostazione `incremental` predefinita, è true).

Per informazioni dettagliate su tutti i parametri, vedere [Parametri della classe snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Creare uno snapshot del volume

Per un esempio di questa funzionalità, creare una classe [di snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) con il comando [kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Si creerà ora uno [snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) dal PVC creato dinamicamente all'inizio di questa [esercitazione,](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Verificare che lo snapshot sia stato creato correttamente:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Creare un nuovo PVC basato su uno snapshot del volume

È possibile creare un nuovo PVC basato su uno snapshot del volume. Usare lo snapshot creato nel passaggio precedente e creare un nuovo [PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) e un [nuovo pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) per usarlo.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Infine, è possibile verificare che sia lo stesso PVC creato in precedenza controllando il contenuto.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Come previsto, è comunque possibile visualizzare il file creato in `test.txt` precedenza.

## <a name="clone-volumes"></a>Clonare volumi

Un volume clonato è definito come duplicato di un volume Kubernetes esistente. Per altre informazioni sulla clonazione di volumi in Kubernetes, vedere la documentazione concettuale per la [clonazione di volumi](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Il driver CSI per i dischi di Azure supporta la clonazione dei volumi. Per una dimostrazione, creare [un volume clonato](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) [del](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) pod creato in `azuredisk-pvc` precedenza e un nuovo pod per [l'utilizzo.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

È ora possibile controllare il contenuto del volume clonato eseguendo il comando seguente e confermando che il `test.txt` file creato è ancora visualizzato.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Ridimensionare un volume permanente

È invece possibile richiedere un volume più grande per un PVC. Modificare l'oggetto PVC e specificare dimensioni maggiori. Questa modifica attiva l'espansione del volume sottostante che è a supporto del PV.

> [!NOTE]
> Non viene mai creato un nuovo PV per soddisfare l'attestazione. Viene invece ridimensionato un volume esistente.

Nel servizio AzureKs la classe di archiviazione predefinita consente già l'espansione, quindi usare il PVC creato in precedenza `managed-csi` con questa classe di [archiviazione](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Il PVC ha richiesto un volume permanente da 10 Gi. È possibile verificarlo eseguendo:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Attualmente, il driver CSI del disco di Azure supporta solo il ridimensionamento di PVC senza pod associati (e il volume non montato in un nodo specifico).

Di conseguenza, eliminare il pod creato in precedenza:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Espandere il PVC aumentando il `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Verificare che il volume sia ora più grande:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> Il PVC rifletterà le nuove dimensioni solo dopo aver associato di nuovo un pod.

Creare un nuovo pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Infine, verificare le dimensioni del PVC e all'interno del pod:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Disco condiviso

[Dischi condivisi di Azure è](../virtual-machines/disks-shared.md) una funzionalità di Azure Managed Disks che consente di collegare un disco di Azure ai nodi agente contemporaneamente. Il collegamento di un disco gestito a più nodi agente consente, ad esempio, di distribuire applicazioni nuove o di eseguire la migrazione di applicazioni cluster esistenti in Azure.

> [!IMPORTANT] 
> Attualmente, solo il dispositivo a blocchi non elaborati ( `volumeMode: Block` ) è supportato dal driver CSI del disco di Azure. Le applicazioni devono gestire il coordinamento e il controllo di scritture, operazioni di lettura, blocchi, cache, montamenti e montaggio sul disco condiviso, esposto come dispositivo a blocchi non elaborato.

Creare un file denominato copiando il comando seguente che contiene la classe di archiviazione `shared-disk.yaml` su disco condiviso e il PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Creare la classe di archiviazione con [il comando kubectl apply][kubectl-apply] e specificare il `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Creare ora un file denominato `deployment-shared.yml` copiando il comando seguente:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Creare la distribuzione con il [comando kubectl apply][kubectl-apply] e specificare il `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Infine, si esamini il dispositivo a blocchi all'interno del pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Contenitori Windows

Il driver CSI del disco di Azure supporta anche i nodi e i contenitori di Windows. Se si vogliono usare contenitori windows, seguire [l'esercitazione sui contenitori di Windows](windows-container-cli.md) per aggiungere un pool di nodi windows.

Dopo aver creato un pool di nodi di Windows, è ora possibile usare le classi di archiviazione incorporate, ad esempio `managed-csi` . È possibile distribuire un set con stato basato su [Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) di esempio che salva i timestamp nel file distribuendo il comando seguente con il `data.txt` comando [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

È ora possibile convalidare il contenuto del volume eseguendo:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare i driver CSI per File di Azure, vedere Usare File di Azure [con i driver CSI.](azure-files-csi.md)
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere [Procedure consigliate per l'archiviazione][operator-best-practices-storage]e i backup in servizio Azure Kubernetes .


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
