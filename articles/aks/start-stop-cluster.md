---
title: Avviare e arrestare un servizio Azure Kubernetes (servizio Web Disattesa)
description: Informazioni su come arrestare o avviare un cluster servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 2d3c946bc2f98b0c06fe33dcaaa77a5399f6d56b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782730"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Arrestare e avviare un cluster servizio Azure Kubernetes (AKS)

I carichi di lavoro del servizio AKS potrebbero non dover essere eseguiti in modo continuo, ad esempio un cluster di sviluppo usato solo durante l'orario di ufficio. Ciò comporta tempi in cui il cluster servizio Azure Kubernetes (AKS) potrebbe essere inattivo, eseguendo non più dei componenti di sistema. È possibile ridurre il footprint del cluster ridimensionando tutti i pool di nodi su [ `User` 0,](scale-cluster.md#scale-user-node-pools-to-0)ma il [ `System` pool](use-system-pools.md) è comunque necessario per eseguire i componenti di sistema mentre il cluster è in esecuzione. Per ottimizzare ulteriormente i costi durante questi periodi, è possibile disattivare completamente (arrestare) il cluster. Questa azione arresterà completamente il piano di controllo e i nodi agente, consentendo di risparmiare su tutti i costi di calcolo, mantenendo tutti gli oggetti e lo stato del cluster archiviati per quando lo si avvia di nuovo. È quindi possibile riprendere il punto in cui si è rimasti dopo un fine settimana o fare in modo che il cluster sia in esecuzione solo durante l'esecuzione dei processi batch.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

### <a name="limitations"></a>Limitazioni

Quando si usa la funzionalità di avvio/arresto del cluster, si applicano le restrizioni seguenti:

- Questa funzionalità è supportata solo per i cluster supportati da set di scalabilità di macchine virtuali.
- Lo stato del cluster di un cluster del servizio Web del servizio Web arrestato viene mantenuto per un massimo di 12 mesi. Se il cluster viene arrestato per più di 12 mesi, lo stato del cluster non può essere ripristinato. Per altre informazioni, vedere Criteri di supporto [del servizio Web Del servizio App.](support-policies.md)
- È possibile avviare o eliminare solo un cluster del servizio Web Del servizio Web arrestato. Per eseguire qualsiasi operazione, ad esempio la scalabilità o l'aggiornamento, avviare prima il cluster.

## <a name="stop-an-aks-cluster"></a>Arrestare un cluster del servizio Servizio Web Di windows

È possibile usare il comando per arrestare i nodi e il piano di controllo di un cluster del servizio Servizio Web Dick in `az aks stop` esecuzione. L'esempio seguente arresta un cluster *denominato myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

È possibile verificare quando il cluster viene arrestato usando il [comando az aks show][az-aks-show] e confermando che `powerState` mostra come `Stopped` nell'output seguente:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se indica `provisioningState` che il cluster non è ancora stato completamente `Stopping` arrestato.

> [!IMPORTANT]
> Se si usano i budget [di interruzione dei pod,](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) l'operazione di arresto può richiedere più tempo, in quanto il completamento del processo di svuotamento richiederà più tempo.

## <a name="start-an-aks-cluster"></a>Avviare un cluster del servizio Web Disassoce

È possibile usare il comando per avviare i nodi e il piano di controllo di un cluster del `az aks start` servizio Web Disatteso arrestato. Il cluster viene riavviato con lo stato del piano di controllo precedente e il numero di nodi agente.  
L'esempio seguente avvia un cluster *denominato myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

È possibile verificare quando il cluster è stato avviato usando il [comando az servizio][az-aks-show] WebKs show e confermando che `powerState` mostra come `Running` nell'output seguente:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se indica `provisioningState` che il cluster non è ancora stato completamente `Starting` avviato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come ridimensionare `User` i pool a 0, vedere [Ridimensionare i pool a `User` 0.](scale-cluster.md#scale-user-node-pools-to-0)
- Per informazioni su come risparmiare sui costi usando le istanze Spot, vedere [Aggiungere un pool di nodi spot al servizio Web Diaks.](spot-node-pool.md)
- Per altre informazioni sui criteri di supporto del servizio Web Diaks, vedere Criteri [di supporto del servizio AKS.](support-policies.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
