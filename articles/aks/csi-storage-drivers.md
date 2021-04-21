---
title: Abilitare i Container Storage Interface (CSI) in servizio Azure Kubernetes (AKS)
description: Informazioni su come abilitare i driver Container Storage Interface (CSI) per dischi di Azure e File di Azure in un cluster servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c9edfdf1c9740ec1fdaaeeedbc6ba92793eb0b3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779958"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Abilitare i Container Storage Interface (CSI) per dischi di Azure e File di Azure in servizio Azure Kubernetes (AKS) (anteprima)

Il Container Storage Interface (CSI) è uno standard per l'esposizione di sistemi di archiviazione di file e blocchi arbitrari a carichi di lavoro in contenitori in Kubernetes. Adottando e usando CSI, servizio Azure Kubernetes (AKS) può scrivere, distribuire e scorrere i plug-in per esporre nuovi sistemi di archiviazione o migliorare quelli esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Il supporto del driver di archiviazione CSI nel servizio AzureKs consente di usare in modo nativo:
- [*Dischi di Azure,*](azure-disk-csi.md)che possono essere usati per creare una risorsa Kubernetes *DataDisk.* I dischi possono usare dischi Archiviazione Premium Azure, supportati da unità SSD ad alte prestazioni o Archiviazione Standard di Azure, supportate da dischi rigidi standard o SSD Standard. Per la maggior parte dei carichi di lavoro di produzione e sviluppo, usare Archiviazione Premium. I dischi di Azure sono montati *come ReadWriteOnce,* quindi sono disponibili solo per un singolo pod. Per i volumi di archiviazione accessibili contemporaneamente da più pod, usare File di Azure.
- [*File di Azure*](azure-files-csi.md), che può essere usato per montare una condivisione SMB 3.0 supportata da un account Archiviazione di Azure nei pod. Con File di Azure, è possibile condividere dati tra più nodi e pod. File di Azure possibile usare Archiviazione Standard di Azure supportati da hdd regolari o azure Archiviazione Premium supportati da unità SSD ad alte prestazioni.

> [!IMPORTANT]
> A partire da Kubernetes versione 1.21, Kubernetes userà solo i driver CSI e per impostazione predefinita. Questi driver sono il futuro del supporto di archiviazione in Kubernetes.
>
> *I driver in-tree* fanno riferimento ai driver di archiviazione correnti che fanno parte del codice Kubernetes principale rispetto ai nuovi driver CSI, che sono plug-in.

## <a name="limitations"></a>Limitazioni

- Questa funzionalità può essere impostata solo in fase di creazione del cluster.
- La versione secondaria minima di Kubernetes che supporta i driver CSI è v1.17.
- Durante l'anteprima, la classe di archiviazione predefinita sarà comunque la stessa classe di archiviazione [nell'albero](concepts-storage.md#storage-classes). Quando questa funzionalità è disponibile a livello generale, la classe di archiviazione predefinita sarà la classe di archiviazione e le classi di archiviazione `managed-csi` nell'albero verranno rimosse.
- Durante la prima fase di anteprima è supportata solo l'interfaccia della riga di comando di Azure.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrare la `EnableAzureDiskFileCSIDriver` funzionalità di anteprima

Per creare un cluster del servizio Azure File di Azure che può usare i driver CSI per i dischi di Azure, è necessario abilitare il `EnableAzureDiskFileCSIDriver` flag di funzionalità nella sottoscrizione.

Registrare `EnableAzureDiskFileCSIDriver` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando [il comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster del servizio AzureKs o un pool di nodi in grado di usare i driver di archiviazione CSI, è necessaria l'estensione più recente dell'interfaccia della riga di comando di Azure *aks-preview.* Installare *l'estensione dell'interfaccia della riga* di comando di Azure aks-preview usando [il comando az extension add.][az-extension-add] Oppure installare eventuali aggiornamenti disponibili usando il [comando az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Creare un nuovo cluster in grado di usare i driver di archiviazione CSI

Creare un nuovo cluster in grado di usare i driver di archiviazione CSI per i dischi di Azure File di Azure usando i comandi dell'interfaccia della riga di comando seguenti. Usare il `--aks-custom-headers` flag per impostare la `EnableAzureDiskFileCSIDriver` funzionalità.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Creare il cluster del servizio AzureKs con il supporto per i driver di archiviazione CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Se si desidera creare cluster nei driver di archiviazione dell'albero anziché nei driver di archiviazione CSI, è possibile omettere il parametro `--aks-custom-headers` personalizzato.


Controllare il numero di volumi basati su disco di Azure che è possibile collegare a questo nodo eseguendo:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Passaggi successivi

- Per usare l'unità CSI per i dischi di Azure, vedere [Usare i dischi di Azure con i driver CSI.](azure-disk-csi.md)
- Per usare l'unità CSI per File di Azure, vedere [Usare File di Azure con i driver CSI.](azure-files-csi.md)
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere Procedure consigliate per l'archiviazione e [i backup in servizio Azure Kubernetes][operator-best-practices-storage].

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