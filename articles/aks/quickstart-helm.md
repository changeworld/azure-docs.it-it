---
title: Sviluppare in servizio Azure Kubernetes (AKS) con Helm
description: Usare Helm con il servizio AKS e Registro Azure Container creare un pacchetto ed eseguire contenitori di applicazioni in un cluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: e293d0c58f265b25f3df0a218f84888467468f59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767494"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Guida introduttiva: Sviluppare in servizio Azure Kubernetes (AKS) con Helm

[Helm][helm] è uno strumento di creazione pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente ai gestori di pacchetti Linux *come APT* e *Yum,* Helm gestisce i grafici Kubernetes, ovvero pacchetti di risorse Kubernetes preconfigurati.

In questa guida introduttiva si userà Helm per creare un pacchetto ed eseguire un'applicazione nel servizio AppKs. Per altre informazioni sull'installazione di un'applicazione esistente con Helm, vedere la guida alla procedura installare le applicazioni esistenti con Helm nel servizio [AKS.][helm-existing]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli).
* [Helm v3 installato.][helm-install]

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container
È necessario archiviare le immagini del contenitore in un Registro Azure Container (ACR) per eseguire l'applicazione nel cluster del servizio Web Diaks usando Helm. Specificare il proprio nome del registro univoco in Azure e contenente da 5 a 50 caratteri alfanumerici. Lo SKU *Basic* è un punto di ingresso con costi ottimali a fini di sviluppo, che assicura l'equilibrio tra spazio di archiviazione e velocità effettiva.

L'esempio seguente usa [az acr create][az-acr-create] per creare un registro di controllo di accesso denominato *MyHelmACR* in *MyResourceGroup* con lo SKU *Basic.*

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

L'output sarà simile all'esempio seguente. Prendere nota del valore *loginServer* per il registro di controllo di accesso perché verrà utilizzato in un passaggio successivo. Nell'esempio seguente *myhelmacr.azurecr.io* è *loginServer* per *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Il nuovo cluster del servizio AKS deve accedere al registro di controllo di accesso per eseguire il pull delle immagini del contenitore ed eseguirle. Usare il comando seguente per:
* Creare un cluster del servizio Web Del servizio App di Microsoft Windows denominato *MyAKS* e *collegare MyHelmACR.*
* Concedere al cluster *MyAKS l'accesso* all'ACR *MyHelmACR.*


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Connettersi al cluster del servizio AKS

Per connettere un cluster Kubernetes in locale, usare il client della riga di comando Kubernetes, [kubectl][kubectl]. `kubectl` è già installato se si usa Azure Cloud Shell. 

1. Eseguire `kubectl` l'installazione in locale usando il `az aks install-cli` comando :

    ```azurecli
    az aks install-cli
    ```

2. Configurare `kubectl` per connettersi al cluster Kubernetes usando il `az aks get-credentials` comando . L'esempio di comando seguente ottiene le credenziali per il cluster del servizio Web Diaks *denominato MyAKS* in *MyResourceGroup:*  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

Questa guida introduttiva [usa un'Node.js di esempio dal repository Azure Dev Spaces di esempio][example-nodejs]. Clonare l'applicazione da GitHub e passare alla `dev-spaces/samples/nodejs/getting-started/webfrontend` directory .

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Creare un Dockerfile

Creare un nuovo file *Dockerfile* usando i comandi seguenti:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Compilare ed eseguire il push dell'applicazione di esempio in Registro Registro Controllo di accesso

Usando il Dockerfile precedente, eseguire il [comando az acr build][az-acr-build] per compilare ed eseguire il push di un'immagine nel registro. Alla `.` fine del comando imposta il percorso del Dockerfile (in questo caso, la directory corrente).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Creare il grafico Helm

Generare il grafico Helm usando il `helm create` comando .

```console
helm create webfrontend
```

Aggiornare *webfrontend/values.yaml:*
* Sostituire il valore loginServer del registro di sistema di cui si è già fatto parte in un passaggio precedente, ad esempio *myhelmacr.azurecr.io*.
* Modificare `image.repository` in `<loginServer>/webfrontend`
* Modificare `service.type` in `LoadBalancer`

Ad esempio:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Eseguire `appVersion` `v1` l'aggiornamento a in *webfrontend/Chart.yaml.* Ad esempio:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Eseguire il chart Helm

Installare l'applicazione usando il grafico Helm usando il `helm install` comando .

```console
helm install webfrontend webfrontend/
```

Ci vogliono alcuni minuti perché il servizio restituisca un indirizzo IP pubblico. Monitorare lo stato di `kubectl get service` avanzamento usando il comando con `--watch` l'argomento .

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Passare al servizio di bilanciamento del carico dell'applicazione in un browser usando `<EXTERNAL-IP>` per visualizzare l'applicazione di esempio.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Usare il [comando az group delete][az-group-delete] per rimuovere il gruppo di risorse, il cluster del servizio Container, il registro contenitori, le immagini del contenitore archiviate nel Registro Container e tutte le risorse correlate.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].
> 
> Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Helm, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
