---
title: Ruotare i certificati in servizio Azure Kubernetes (servizio Web AKS)
description: Informazioni su come ruotare i certificati in un cluster servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 6baad681a9d629c397c53ab90057cc5746fc3b85
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776016"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Ruotare i certificati in servizio Azure Kubernetes (servizio Web AKS)

servizio Azure Kubernetes usa i certificati per l'autenticazione con molti dei relativi componenti. Periodicamente, potrebbe essere necessario ruotare i certificati per motivi di sicurezza o criteri. Ad esempio, potrebbe essere necessario un criterio per ruotare tutti i certificati ogni 90 giorni.

Questo articolo illustra come ruotare i certificati nel cluster del servizio Web Diaks.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.77 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificati del servizio AKS, autorità di certificazione e account di servizio

Il servizio AKS genera e usa i certificati, le autorità di certificazione e gli account di servizio seguenti:

* Il server API del servizio Gateway Gateway crea un'autorità di certificazione (CA) denominata CA cluster.
* Il server API ha una CA cluster, che firma i certificati per la comunicazione unidirezionale dal server API ai kubelets.
* Ogni kubelet crea anche una richiesta di firma del certificato (CSR), firmata dalla CA cluster, per la comunicazione dal kubelet al server API.
* L'aggregatore di API usa la CA cluster per rilasciare certificati per la comunicazione con altre API. L'aggregatore di API può anche avere una propria CA per il rilascio di tali certificati, ma attualmente usa la CA cluster.
* Ogni nodo usa un token di account di servizio (SA), firmato dalla CA cluster.
* Il client dispone di un certificato per la comunicazione con il cluster del servizio `kubectl` Web Disatteso.

> [!NOTE]
> I cluster del servizio AKS creati prima di marzo 2019 hanno certificati che scadono dopo due anni. Qualsiasi cluster creato dopo marzo 2019 o qualsiasi cluster con i certificati ruotati ha certificati ca cluster che scadono dopo 30 anni. Tutti gli altri certificati scadono dopo due anni. Per verificare quando è stato creato il cluster, usare `kubectl get nodes` per visualizzare *l'età* dei pool di nodi.
> 
> È anche possibile controllare la data di scadenza del certificato del cluster. Ad esempio, il comando Bash seguente visualizza i dettagli del certificato per il cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Ruotare i certificati del cluster

> [!WARNING]
> La rotazione dei certificati tramite può causare fino a 30 minuti di inattività per il cluster del servizio `az aks rotate-certs` Web Diaks.

Usare [il comando az aks get-credentials][az-aks-get-credentials] per accedere al cluster del servizio Web Diaz. Questo comando scarica e configura anche il `kubectl` certificato client nel computer locale.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Usare `az aks rotate-certs` per ruotare tutti i certificati, le CA e le CA nel cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Il completamento può richiedere fino a 30 `az aks rotate-certs` minuti. Se il comando non riesce prima del completamento, usare per verificare che `az aks show` lo stato del cluster sia Rotazione dei *certificati*. Se il cluster si trova in uno stato di errore, rieseguire `az aks rotate-certs` per ruotare di nuovo i certificati.

Verificare che i certificati non siano più validi eseguendo un `kubectl` comando . Poiché i certificati usati da non sono stati `kubectl` aggiornati, verrà visualizzato un errore.  Ad esempio:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aggiornare il certificato usato `kubectl` eseguendo `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verificare che i certificati siano stati aggiornati eseguendo un `kubectl` comando che avrà ora esito positivo. Ad esempio:

```console
kubectl get no
```

> [!NOTE]
> Se si dispone di servizi eseguiti in AKS, ad esempio [Azure Dev Spaces][dev-spaces], potrebbe essere necessario aggiornare anche i certificati correlati [a][dev-spaces-rotate] tali servizi.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come ruotare automaticamente i certificati, le CA e le CA del cluster. Per altre informazioni [sulle procedure consigliate per la][aks-best-practices-security-upgrades] sicurezza del servizio Servizio Azure Kubernetes, vedere Procedure consigliate per la sicurezza e gli aggiornamenti del cluster nel servizio Servizio Azure Kubernetes.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
