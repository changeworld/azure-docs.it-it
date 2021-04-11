---
title: Concetti - Archiviazione nel servizio Azure Kubernetes
description: Informazioni sull'archiviazione nel servizio Azure Kubernetes, inclusi volumi, volumi permanenti, classi di archiviazione e attestazioni
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105783"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes

Per le applicazioni in esecuzione in Azure Kubernetes Service (AKS) potrebbe essere necessario archiviare e recuperare i dati. Anche se alcuni carichi di lavoro dell'applicazione possono usare l'archiviazione locale e veloce su nodi non necessari e vuoti, altri richiedono l'archiviazione che viene mantenute su volumi di dati più normali all'interno della piattaforma Azure. 

Potrebbe essere necessario eseguire più POD:
* Condividono gli stessi volumi di dati. 
* Alleghi i volumi di dati se il Pod viene ripianificato in un nodo diverso. 

Infine, potrebbe essere necessario inserire i dati sensibili o le informazioni di configurazione delle applicazioni nei pod.

Questo articolo introduce i concetti di base per rendere disponibili risorse di archiviazione per le applicazioni nel servizio Azure Kubernetes:

- [Volumi](#volumes)
- [Volumi permanenti](#persistent-volumes)
- [Classi di archiviazione](#storage-classes)
- [Attestazioni di volume permanente](#persistent-volume-claims)

![Opzioni di archiviazione per le applicazioni in un cluster del servizio Azure Kubernetes](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Volumi

Kubernetes in genere considera i singoli pod come risorse effimere e monouso. Le applicazioni hanno approcci diversi a loro disposizione per l'uso e il salvataggio permanente dei dati. Un *volume* rappresenta un modo per archiviare, recuperare e salvare i dati tra i pod e per l'intero ciclo di vita dell'applicazione.

I volumi tradizionali vengono creati come risorse Kubernetes supportate da archiviazione di Azure. È possibile creare manualmente i volumi di dati da assegnare direttamente ai pod o fare in modo che Kubernetes li crei automaticamente. I volumi di dati possono usare dischi di Azure o File di Azure.

### <a name="azure-disks"></a>Dischi di Azure

Usare *dischi di Azure* per creare una risorsa Kubernetes *DataDisk* . I dischi possono usare:
* Archiviazione Premium di Azure, supportata da unità SSD a prestazioni elevate o 
* Archiviazione standard di Azure, supportata da HDD normali. 

> [!TIP]
>Per la maggior parte dei carichi di lavoro di produzione e di sviluppo, usare l'archiviazione Premium. 

Poiché i dischi di Azure sono montati come *ReadWriteOnce*, sono disponibili solo per un singolo POD. Per i volumi di archiviazione a cui è possibile accedere simultaneamente da più POD, utilizzare File di Azure.

### <a name="azure-files"></a>File di Azure
Usare *file di Azure* per montare una condivisione SMB 3,0 supportata da un account di archiviazione di Azure per i pod. I file consentono di condividere i dati tra più nodi e Pod e possono usare:
* Archiviazione Premium di Azure, supportata da unità SSD a prestazioni elevate o 
* Archiviazione standard di Azure supportata da HDD normali.

### <a name="volume-types"></a>Tipi di volume
I volumi Kubernetes rappresentano solo un disco tradizionale per l'archiviazione e il recupero delle informazioni. I volumi di Kubernetes possono anche essere usati come modo per inserire dati in un pod per l'uso da parte dei contenitori. 

I tipi di volume comuni in Kubernetes includono:

#### <a name="emptydir"></a>emptyDir

Utilizzato comunemente come spazio temporaneo per un pod. Tutti i contenitori all'interno di un pod possono accedere ai dati nel volume. I dati scritti in questo tipo di volume vengono mantenuti solo per la durata del Pod. Quando si elimina il Pod, il volume viene eliminato. Questo volume USA in genere l'archiviazione su disco del nodo locale sottostante, sebbene possa esistere anche solo nella memoria del nodo.

#### <a name="secret"></a>secret

È possibile usare i volumi *segreti* per inserire dati sensibili in pod, ad esempio le password. 
1. Creare un segreto usando l'API Kubernetes. 
1. Definire il pod o la distribuzione e richiedere un segreto specifico. 
    * I segreti vengono forniti solo ai nodi con un pod pianificato che li richiede.
    * Il segreto viene archiviato in *tmpfs*, non scritto su disco. 
1. Quando si elimina l'ultimo POD in un nodo che richiede un segreto, il segreto viene eliminato dal tmpfs del nodo. 
   * I segreti vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo dai pod all'interno dello stesso spazio dei nomi.

#### <a name="configmap"></a>configMap

È possibile usare *configMap* per inserire le proprietà delle coppie chiave-valore in pod, ad esempio le informazioni di configurazione dell'applicazione. Definire le informazioni di configurazione dell'applicazione come risorsa Kubernetes, facilmente aggiornabili e applicate alle nuove istanze di Pod durante la distribuzione. 

Come per l'uso di un segreto:
1. Creare una ConfigMap usando l'API Kubernetes. 
1. Richiedere il ConfigMap quando si definisce un pod o una distribuzione. 
   * I volumi ConfigMap vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo da pod all'interno dello stesso spazio dei nomi.

## <a name="persistent-volumes"></a>Volumi permanenti

I volumi definiti e creati come parte del ciclo di vita del pod sono disponibili solo fino a quando non si elimina il pod. I pod si aspettano in genere che le rispettive risorse di archiviazione rimangano disponibili se un pod viene ripianificato su un host diverso durante un evento di manutenzione, in particolare in StatefulSets. Un *volume permanente* è una risorsa di archiviazione creata e gestita dall'API di Kubernetes che può esistere oltre la durata di un singolo pod.

È possibile usare i dischi o i file di Azure per fornire i PersistentVolume. Come indicato nella sezione [volumi](#volumes) , la scelta di dischi o file è spesso determinata dalla necessità di accesso simultaneo ai dati o al livello di prestazioni.

![Volumi permanenti in un cluster del servizio Azure Kubernetes](media/concepts-storage/persistent-volumes.png)

Un volume permanente può essere creato *staticamente* da un amministratore del cluster oppure *dinamicamente* dal server dell'API di Kubernetes. Se è pianificato un pod e le richieste di archiviazione non sono attualmente disponibili, Kubernetes può creare il disco di Azure sottostante o l'archiviazione dei file e collegarla al Pod. Il provisioning dinamico usa una *StorageClass* per identificare il tipo di archiviazione di Azure da creare.

## <a name="storage-classes"></a>Classi di archiviazione

Per definire livelli di archiviazione diversi, ad esempio Premium e Standard, è possibile creare una *StorageClass*. 

La StorageClass definisce anche i *reclaimPolicy*. Quando si elimina il pod e il volume permanente non è più necessario, reclaimPolicy controlla il comportamento della risorsa di archiviazione di Azure sottostante. La risorsa di archiviazione sottostante può essere eliminata o mantenuta per l'uso con un pod futuro.

In AKS `StorageClasses` vengono creati quattro iniziali per il cluster con i plug-in di archiviazione nell'albero:

| Autorizzazione | Motivo |
|---|---|
| `default` | Usa l'archiviazione StandardSSD di Azure per creare un disco gestito. Il criterio di rimborso garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il volume permanente che lo ha usato. |
| `managed-premium` | Usa archiviazione Premium di Azure per creare un disco gestito. Il criterio di rimborso garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il volume permanente che lo ha usato. |
| `azurefile` | Usa l'archiviazione standard di Azure per creare una condivisione file di Azure. Il criterio di rimborso garantisce che la condivisione file di Azure sottostante venga eliminata quando viene eliminato il volume permanente che lo ha usato. |
| `azurefile-premium` | Usa archiviazione Premium di Azure per creare una condivisione file di Azure. Il criterio di rimborso garantisce che la condivisione file di Azure sottostante venga eliminata quando viene eliminato il volume permanente che lo ha usato.|

Per i cluster che usano i nuovi plug-in dell'interfaccia di archiviazione contenitori (CSI), vengono creati i seguenti elementi aggiuntivi `StorageClasses` :

| Autorizzazione | Motivo |
|---|---|
| `managed-csi` | Usa l'archiviazione con ridondanza locale di Azure StandardSSD (con ridondanza locale) per creare un disco gestito. Il criterio di rimborso garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il volume permanente che lo ha usato. La classe di archiviazione configura anche i volumi permanenti in modo che siano espandibili. è sufficiente modificare l'attestazione del volume permanente con le nuove dimensioni. |
| `managed-csi-premium` | Usa l'archiviazione con ridondanza locale di Azure Premium (con ridondanza locale) per creare un disco gestito. Il criterio di rimborso garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il volume permanente che lo ha usato. Analogamente, questa classe di archiviazione consente l'espansione dei volumi permanenti. |
| `azurefile-csi` | Usa l'archiviazione standard di Azure per creare una condivisione file di Azure. Il criterio di rimborso garantisce che la condivisione file di Azure sottostante venga eliminata quando viene eliminato il volume permanente che lo ha usato. |
| `azurefile-csi-premium` | Usa archiviazione Premium di Azure per creare una condivisione file di Azure. Il criterio di rimborso garantisce che la condivisione file di Azure sottostante venga eliminata quando viene eliminato il volume permanente che lo ha usato.|

A meno che non si specifichi un StorageClass per un volume permanente, verrà usato il valore predefinito StorageClass. Assicurarsi che i volumi usino lo spazio di archiviazione appropriato necessario per la richiesta di volumi permanenti. 

È possibile creare una StorageClass per esigenze aggiuntive con `kubectl`. L'esempio seguente usa Managed Disks Premium e specifica che il disco di Azure sottostante deve essere *mantenuto* quando si elimina il pod:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS riconcilia le classi di archiviazione predefinite e sovrascriverà le modifiche apportate alle classi di archiviazione.

## <a name="persistent-volume-claims"></a>Attestazioni di volume permanente

Una PersistentVolumeClaim richiede risorse di archiviazione su disco o file con StorageClass, modalità di accesso e dimensioni particolari. Il server API Kubernetes può eseguire il provisioning dinamico della risorsa di archiviazione di Azure sottostante se nessuna risorsa esistente può soddisfare l'attestazione in base al StorageClass definito. 

La definizione del pod include il montaggio del volume dopo la connessione del volume al pod.

![Attestazioni di volume permanente in un cluster del servizio Azure Kubernetes](media/concepts-storage/persistent-volume-claims.png)

Dopo che una risorsa di archiviazione disponibile è stata assegnata al pod che richiede l'archiviazione, PersistentVolume è *associato* a un PersistentVolumeClaim. Il mapping di volumi permanenti a attestazioni è 1:1.

Il manifesto YAML di esempio seguente mostra un'attestazione di volume permanente che usa la StorageClass *managed-premium* e richiede un disco *5Gi*:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Quando si crea una definizione di Pod, è necessario specificare anche:
* Attestazione del volume permanente per richiedere lo spazio di archiviazione desiderato. 
* *VolumeMount* per le applicazioni per la lettura e la scrittura dei dati. 

Il manifesto YAML di esempio seguente mostra come l'attestazione di volume permanente precedente può essere usata per montare un volume in */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Per montare un volume in un contenitore di Windows, specificare la lettera di unità e il percorso. Ad esempio:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes][operator-best-practices-storage].

Per informazioni su come creare i volumi dinamici e statici che usano Dischi di Azure o File di Azure, vedere gli articoli di procedure seguenti:

- [Creare un volume statico usando Dischi di Azure][aks-static-disks]
- [Creare un volume statico usando File di Azure][aks-static-files]
- [Creare un volume dinamico usando Dischi di Azure][aks-dynamic-disks]
- [Creare un volume dinamico usando File di Azure][aks-dynamic-files]

Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes/Cluster e carichi di lavoro del servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Kubernetes/Identità del servizio Azure Kubernetes][aks-concepts-identity]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
