---
title: Interagire con contenitori Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Informazioni su come eseguire Azure Dev Spaces in un cluster esistente con contenitori Windows
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Kubernetes, contenitori, contenitori Windows
ms.openlocfilehash: bbef5eafe44e38691327714c14c6a6026d45a3c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777438"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagire con i contenitori di Windows usando Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

È possibile abilitare Azure Dev Spaces in spazi dei nomi Kubernetes nuovi ed esistenti. Azure Dev Spaces eseguirà e instrumenterà i servizi eseguiti in contenitori Linux. Questi servizi possono anche interagire con le applicazioni eseguite in contenitori Windows nello stesso spazio dei nomi. Questo articolo illustra come usare Dev Spaces per eseguire servizi in uno spazio dei nomi con contenitori Windows esistenti. Al momento non è possibile eseguire il debug o connettersi a contenitori Windows con Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Configurare il cluster

Questo articolo presuppone che sia già presente un cluster con pool di nodi Sia Linux che Windows. Se è necessario creare un cluster con pool di nodi Linux e Windows, è possibile seguire le istruzioni riportate [qui.][windows-container-cli]

Connettersi al cluster usando [kubectl][kubectl], il client della riga di comando di Kubernetes. Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'output di esempio seguente mostra un cluster con un nodo Windows e Linux. Prima di procedere, verificare che *lo stato sia Pronto* per ogni nodo.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Applicare un [taint][using-taints] ai nodi di Windows. Il taint nei nodi Windows impedisce a Dev Spaces di pianificare l'esecuzione di contenitori Linux nei nodi Windows. Il comando di esempio di comando seguente applica un taint al nodo Windows *aksnpwin987654* dell'esempio precedente.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando si applica un taint a un nodo, è necessario configurare una tolleranza corrispondente nel modello di distribuzione del servizio per eseguire il servizio in tale nodo. L'applicazione di esempio è già configurata con [una tolleranza corrispondente][sample-application-toleration-example] al taint configurato nel comando precedente.

## <a name="run-your-windows-service"></a>Eseguire il servizio Windows

Eseguire il servizio Windows nel cluster del servizio AKS e verificare che sia in *stato In* esecuzione. Questo articolo usa [un'applicazione di esempio][sample-application] per illustrare un servizio Windows e Linux in esecuzione nel cluster.

Clonare l'applicazione di esempio da GitHub e passare alla `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` directory:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

L'applicazione di esempio [usa Helm 3][helm-installed] per eseguire il servizio Windows nel cluster. Passare alla `charts` directory e usare Helm per eseguire il servizio Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Il comando precedente usa Helm per eseguire il servizio Windows nello spazio *dei nomi dev.* Se non si dispone di uno spazio dei nomi denominato *dev*, verrà creato.

Usare il `kubectl get pods` comando per verificare che il servizio Windows sia in esecuzione nel cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Abilitare Azure Dev Spaces

Abilitare Dev Spaces nello stesso spazio dei nomi usato per eseguire il servizio Windows. Il comando seguente abilita Dev Spaces nello spazio dei *nomi dev:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aggiornare il servizio Windows per Dev Spaces

Quando si abilita Dev Spaces in uno spazio dei nomi esistente con contenitori già in esecuzione, per impostazione predefinita Dev Spaces tenterà di instrumentare eventuali nuovi contenitori eseguiti in tale spazio dei nomi. Dev Spaces tenterà anche di instrumentare i nuovi contenitori creati per il servizio già in esecuzione nello spazio dei nomi. Per impedire a Dev Spaces di instrumentare un contenitore in esecuzione nello spazio dei nomi, aggiungere *l'intestazione no-proxy* a `deployment.yaml` .

Aggiungere `azds.io/no-proxy: "true"` al `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Usare `helm list` per elencare la distribuzione per il servizio Windows:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

Nell'esempio precedente il nome della distribuzione è *windows-service*. Aggiornare il servizio Windows con la nuova configurazione usando `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Poiché è stato aggiornato `deployment.yaml` , Dev Spaces non tenterà di instrumentare il servizio.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Eseguire l'applicazione Linux con Azure Dev Spaces

Passare alla `webfrontend` directory e usare i comandi e per eseguire `azds prep` `azds up` l'applicazione Linux nel cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Il `azds prep --enable-ingress` comando genera il grafico Helm e i Dockerfile per l'applicazione.

> [!TIP]
> Il [Dockerfile e il grafico Helm](../how-dev-spaces-works-prep.md#prepare-your-code) per il progetto vengono usati da Azure Dev Spaces per compilare ed eseguire il codice, ma è possibile modificare questi file se si vuole cambiare il modo in cui il progetto viene compilato ed eseguito.

Il `azds up` comando esegue il servizio nello spazio dei nomi .

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

È possibile visualizzare il servizio in esecuzione aprendo l'URL pubblico, visualizzato nell'output del comando azds up. In questo esempio l'URL pubblico è `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Passare al servizio in un browser e fare clic su *Informazioni* in alto. Verificare che sia visualizzato un messaggio dal *servizio mywebapi* contenente la versione di Windows in uso nel contenitore.

![App di esempio che mostra la versione di Windows da mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
