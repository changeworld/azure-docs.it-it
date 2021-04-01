---
title: Installare le applicazioni esistenti con Helm in AKS
description: Informazioni su come usare lo strumento di creazione di pacchetti Helm per distribuire i contenitori in un cluster Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779168"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installare le applicazioni esistenti con Helm in Azure Kubernetes Service (AKS)

[Helm][helm] è uno strumento di creazione di pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente ai gestori di pacchetti Linux, ad esempio *apt* e *yum*, Helm viene usato per gestire i grafici Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come configurare e usare Helm in un cluster Kubernetes nel servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata l'interfaccia della riga di comando di Helm, ovvero il client in esecuzione nel sistema di sviluppo. Consente di avviare, arrestare e gestire le applicazioni con Helm. Se si usa Azure Cloud Shell, l'interfaccia della riga di comando di Helm è già installata. Per le istruzioni di installazione sulla piattaforma locale, vedere [installazione di Helm][helm-install].

> [!IMPORTANT]
> Helm è progettato per l'esecuzione su nodi Linux. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Helm siano pianificati per l'esecuzione solo nei nodi Linux. È anche necessario assicurarsi che tutti i grafici Helm installati siano pianificati per l'esecuzione nei nodi corretti. I comandi di questo articolo usano i [selettori di nodo] [K8S-node-Selector] per assicurarsi che i pod siano pianificati per i nodi corretti, ma non tutti i grafici Helm possono esporre un selettore di nodo. È anche possibile prendere in considerazione l'uso di altre opzioni nel cluster, ad esempio [macchie][taints].

## <a name="verify-your-version-of-helm"></a>Verificare la versione di Helm

Usare il `helm version` comando per verificare che sia installato Helm 3:

```console
helm version
```

L'esempio seguente mostra la versione di Helm 3.0.0 installata:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Installare un'applicazione con Helm V3

### <a name="add-helm-repositories"></a>Aggiungere repository Helm

Usare il comando [Helm repo][helm-repo-add] per aggiungere il repository *ingress-nginx* .

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Trovare i grafici Helm

I grafici Helm vengono usati per distribuire applicazioni in un cluster Kubernetes. Per cercare i grafici Helm già creati, usare il comando [Helm Search][helm-search] :

```console
helm search repo ingress-nginx
```

L'output di esempio sintetico seguente mostra alcuni dei grafici di Helm disponibili per l'uso:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Per aggiornare l'elenco dei grafici, usare il comando [helm repo update][helm-repo-update].

```console
helm repo update
```

L'esempio seguente illustra un aggiornamento riuscito del repository:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Eseguire i grafici Helm

Per installare i grafici con Helm, utilizzare il comando [Helm install][helm-install-command] e specificare il nome della versione e il nome del grafico da installare. Per visualizzare l'installazione di un grafico Helm in azione, è necessario installare una distribuzione nginx di base usando un grafico Helm.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

L'output di esempio sintetico seguente mostra lo stato della distribuzione delle risorse Kubernetes create dal grafico Helm:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Usare il `kubectl get services` comando per ottenere l' *indirizzo IP esterno* del servizio.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Ad esempio, il comando seguente mostra *External-IP* per il servizio *My-nginx-ingress-ingress-nginx-controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Elenca versioni

Per visualizzare un elenco delle versioni installate nel cluster, usare il `helm list` comando.

```console
helm list
```

L'esempio seguente illustra la versione *My-nginx-ingress* distribuita nel passaggio precedente:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Pulire le risorse

Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, usare il comando [Helm Uninstall][helm-cleanup] e specificare il nome della versione, come riportato nel comando precedente `helm list` .

```console
helm uninstall my-nginx-ingress
```

L'esempio seguente mostra che la versione denominata *My-nginx-ingress* è stata disinstallata:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione delle applicazioni Kubernetes con Helm, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
