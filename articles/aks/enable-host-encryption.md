---
title: Abilitare la crittografia basata su host in Azure Kubernetes Service (AKS)
description: Informazioni su come configurare una crittografia basata su host in un cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ac28c698a766f1f3febaff582038906f658d58dd
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071851"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Crittografia basata su host in Azure Kubernetes Service (AKS) (anteprima)

Con la crittografia basata su host, i dati archiviati nell'host VM delle macchine virtuali dei nodi dell'agente AKS vengono crittografati a riposo e i flussi vengono crittografati nel servizio di archiviazione. Ciò significa che i dischi temporanei vengono crittografati a riposo con chiavi gestite dalla piattaforma. La cache del sistema operativo e i dischi dati vengono crittografati a riposo con chiavi gestite dalla piattaforma o chiavi gestite dal cliente, a seconda del tipo di crittografia impostato su tali dischi. Per impostazione predefinita, quando si usa AKS, i dischi del sistema operativo e dei dati vengono crittografati a riposo con chiavi gestite dalla piattaforma, ovvero le cache per questi dischi sono anche per impostazione predefinita crittografate a riposo con chiavi gestite dalla piattaforma.  È possibile specificare le proprie chiavi gestite [usando BYOK (Bring your own key) con dischi di Azure in Azure Kubernetes Service](azure-disk-customer-managed-keys.md). Anche la cache per questi dischi verrà crittografata usando la chiave specificata in questo passaggio.


## <a name="before-you-begin"></a>Prima di iniziare

Questa funzionalità può essere impostata solo in fase di creazione del cluster o di creazione del pool di nodi.

> [!NOTE]
> La crittografia basata su host è disponibile nelle [aree di Azure][supported-regions] che supportano la crittografia lato server di Azure Managed disks e solo con dimensioni specifiche per le [macchine virtuali supportate][supported-sizes].

### <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che sia `aks-preview` installata l'estensione CLI v 0.4.73 o versione successiva.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster AKS che esegue la crittografia basata su host, è necessaria la versione più recente dell'estensione dell'interfaccia della riga di comando *AKS-Preview* . Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] oppure verificare la disponibilità di eventuali aggiornamenti tramite il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitazioni

- Può essere abilitato solo nei nuovi pool di nodi o in nuovi cluster.
- Può essere abilitato solo nelle [aree di Azure][supported-regions] che supportano la crittografia lato server di Azure Managed disks e solo con specifiche [dimensioni di VM supportate][supported-sizes].
- Richiede un cluster AKS e un pool di nodi in base ai set di scalabilità di macchine virtuali (VMSS) come *tipo di set di VM*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Usare la crittografia basata su host nei nuovi cluster (anteprima)

Configurare i nodi dell'agente cluster per utilizzare la crittografia basata su host quando viene creato il cluster. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se si desidera creare cluster senza crittografia basata su host, è possibile omettere il `--enable-encryption-at-host` parametro.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Usare la crittografia basata su host nei cluster esistenti (anteprima)

È possibile abilitare la crittografia basata su host nei cluster esistenti aggiungendo un nuovo pool di nodi al cluster. Configurare un nuovo pool di nodi per l'utilizzo della crittografia basata su host tramite il `--enable-encryption-at-host` parametro.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se si desidera creare nuovi pool di nodi senza la funzionalità di crittografia basata su host, è possibile omettere il `--enable-encryption-at-host` parametro.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le [procedure consigliate per la sicurezza del cluster AKS][best-practices-security] leggere altre informazioni sulla [crittografia basata su host](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
