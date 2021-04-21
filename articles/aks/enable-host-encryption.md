---
title: Abilitare la crittografia basata su host in servizio Azure Kubernetes (AKS)
description: Informazioni su come configurare una crittografia basata su host in un cluster servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772920"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Crittografia basata su host in servizio Azure Kubernetes (anteprima)

Con la crittografia basata su host, i dati archiviati nell'host della macchina virtuale delle macchine virtuali dei nodi dell'agente del servizio Azure Service Pack vengono crittografati quando sono in pausa e vengono crittografati nel servizio di archiviazione. Ciò significa che i dischi temporanei vengono crittografati quando sono in pausa con chiavi gestite dalla piattaforma. La cache dei dischi dati e del sistema operativo viene crittografata in stato di inquieto con chiavi gestite dalla piattaforma o chiavi gestite dal cliente a seconda del tipo di crittografia impostato su tali dischi. Per impostazione predefinita, quando si usa il servizio Azure Online, i dischi dati e del sistema operativo vengono crittografati quando sono in pausa con chiavi gestite dalla piattaforma, vale a dire che anche le cache per questi dischi vengono crittografate per impostazione predefinita con chiavi gestite dalla piattaforma.  È possibile specificare le proprie chiavi gestite seguendo la procedura [BYOK (Bring Your Own Key) con i dischi di Azure in servizio Azure Kubernetes](azure-disk-customer-managed-keys.md). La cache per questi dischi verrà quindi crittografata anche usando la chiave specificata in questo passaggio.


## <a name="before-you-begin"></a>Prima di iniziare

Questa funzionalità può essere impostata solo al momento della creazione del cluster o del pool di nodi.

> [!NOTE]
> La crittografia basata su host è disponibile nelle aree [di Azure][supported-regions] che supportano la crittografia lato server dei dischi gestiti di Azure e solo con dimensioni di VM [supportate specifiche.][supported-sizes]

### <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che sia installata `aks-preview` l'estensione dell'interfaccia della riga di comando v0.4.73 o versione successiva.
- Assicurarsi che il `EnableEncryptionAtHostPreview` flag di funzionalità sia abilitato in `Microsoft.ContainerService` .

È necessario abilitare la funzionalità per la sottoscrizione prima di usare la proprietà EncryptionAtHost per il cluster servizio Azure Kubernetes. Seguire questa procedura per abilitare la funzionalità per la sottoscrizione:

1. Eseguire il comando seguente per registrare la funzionalità per la sottoscrizione

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. Prima di provare la funzionalità, verificare che lo stato della registrazione sia Registrato (richiede alcuni minuti) usando il comando seguente.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster del servizio Web Diaks con crittografia basata su host, è necessaria l'estensione più recente dell'interfaccia della riga di comando *aks-preview.* Installare *l'estensione dell'interfaccia della* riga di comando di Azure aks-preview usando il [comando az extension add][az-extension-add] oppure verificare la presenza di eventuali aggiornamenti disponibili usando il comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitazioni

- Può essere abilitato solo nei nuovi pool di nodi.
- Può essere abilitato solo nelle aree [di Azure][supported-regions] che supportano la crittografia lato server dei dischi gestiti di Azure e solo con dimensioni di macchine virtuali [supportate specifiche.][supported-sizes]
- Sono necessari un cluster del servizio Servizio Gestione servizio Web e un pool di nodi basati su set di scalabilità di macchine virtuali (VMSS) come *set di macchine virtuali di tipo*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Usare la crittografia basata su host nei nuovi cluster (anteprima)

Configurare i nodi dell'agente cluster per l'uso della crittografia basata su host quando viene creato il cluster. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se si desidera creare cluster senza crittografia basata su host, è possibile farlo omettendo il `--enable-encryption-at-host` parametro .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Usare la crittografia basata su host nei cluster esistenti (anteprima)

È possibile abilitare la crittografia basata su host nei cluster esistenti aggiungendo un nuovo pool di nodi al cluster. Configurare un nuovo pool di nodi per l'uso della crittografia basata su host usando il `--enable-encryption-at-host` parametro .

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se si desidera creare nuovi pool di nodi senza la funzionalità di crittografia basata su host, è possibile farlo omettendo il `--enable-encryption-at-host` parametro .

## <a name="next-steps"></a>Passaggi successivi

Esaminare [le procedure consigliate per la sicurezza del cluster del servizio Web][best-practices-security] Di seguito Altre informazioni sulla crittografia basata su [host.](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
