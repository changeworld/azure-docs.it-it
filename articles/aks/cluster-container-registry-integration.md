---
title: Integrare Registro Azure Container con servizio Azure Kubernetes
description: Informazioni su come integrare servizio Azure Kubernetes (AKS) con Registro Azure Container (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: ab8065a14aac9e798bfe7d632aa5b33c44706190
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775829"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Eseguire l'autenticazione con Registro Azure Container dal servizio Azure Kubernetes

Quando si usa Registro Azure Container con il servizio Azure Kubernetes, è necessario definire un meccanismo di autenticazione. Questa operazione viene implementata nell'ambito dell'esperienza dell'interfaccia della riga di comando e del portale concedendo le autorizzazioni necessarie a ACR. Questo articolo fornisce esempi per la configurazione dell'autenticazione tra questi due servizi di Azure. 

È possibile configurare l'integrazione del servizio AzureKs per Il Servizio AzureCr in pochi semplici comandi con l'interfaccia della riga di comando di Azure. Questa integrazione assegna il ruolo AcrPull all'identità gestita associata al cluster del servizio Web Diaks.

> [!NOTE]
> Questo articolo illustra l'autenticazione automatica tra il servizio Servizio Dink e Il Servizio Dicr. Se è necessario eseguire il pull di un'immagine da un registro esterno privato, usare un segreto [di pull dell'immagine.][Image Pull Secret]

## <a name="before-you-begin"></a>Prima di iniziare

Gli esempi presentano i requisiti seguenti:

* **Ruolo di** proprietario **o amministratore dell'account Azure** nella sottoscrizione di **Azure**
* Interfaccia della riga di comando di Azure versione 2.7.0 o successiva

Per evitare la necessità di un **ruolo** di proprietario o amministratore **dell'account Azure,** è possibile configurare manualmente un'identità gestita o usare un'identità gestita esistente per autenticare Il Record di controllo di accesso dal servizio AzureKs. Per altre informazioni, vedere Usare [un'identità gestita di Azure per eseguire l'autenticazione in un Registro Azure Container.](../container-registry/container-registry-authentication-managed-identity.md)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Creare un nuovo cluster del servizio Gestione risorse di Servizio Web con l'integrazione di Registro Registro Microsoft

È possibile configurare l'integrazione di AKS e ACR durante la creazione iniziale del cluster del servizio Web Diaks.  Per consentire a un cluster del servizio Web Del servizio Web Di interagire con Registro Registro Azure Active Directory viene **usata un'identità gestita.** Il comando dell'interfaccia della riga di comando seguente consente di autorizzare un record di controllo di accesso esistente nella sottoscrizione e di configurare il ruolo **ACRPull** appropriato per l'identità gestita. Specificare i valori validi per i parametri riportati di seguito.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

In alternativa, è possibile specificare il nome del Record di controllo di accesso usando un ID risorsa di Record di controllo di accesso con il formato seguente:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Se si usa un record di controllo di accesso che si trova in una sottoscrizione diversa dal cluster del servizio AzureKs, usare l'ID risorsa di Registro Azure DataCr durante il collegamento o lo scollegamento da un cluster del servizio AzureKs.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Il completamento di questo passaggio può richiedere alcuni minuti.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurare l'integrazione ACR per i cluster del servizio AKS esistenti

Integrare un registro di controllo di accesso esistente con i cluster del servizio Web del servizio Web esistenti fornendo valori validi per **acr-name** o **acr-resource-id,** come indicato di seguito.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

o

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

È anche possibile rimuovere l'integrazione tra un registro ACR e un cluster del servizio AKS con le opzioni seguenti

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

oppure

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Uso del servizio ACR & servizio Web di Microsoft &

### <a name="import-an-image-into-your-acr"></a>Importare un'immagine nel record di controllo di acr

Importare un'immagine dall'hub Docker nel registro di controllo di accesso eseguendo le operazioni seguenti:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuire l'immagine di esempio dal servizio Di controllo di accesso al servizio Web Dia

Assicurarsi di avere le credenziali del servizio Web di Servizio Web Disatteso appropriate

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Creare un file denominato **acr-nginx.yaml** contenente gli elementi seguenti. Sostituire il nome della risorsa del Registro di sistema **con acr-name**. Esempio: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Eseguire quindi questa distribuzione nel cluster del servizio AKS:

```console
kubectl apply -f acr-nginx.yaml
```

È possibile monitorare la distribuzione eseguendo:

```console
kubectl get pods
```

Dovrebbero essere disponibili due pod in esecuzione.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Risoluzione dei problemi
* Eseguire il [comando az aks check-acr](/cli/azure/aks#az_aks_check_acr) per verificare che il Registro di sistema sia accessibile dal cluster del servizio Web Diaks.
* Altre informazioni sulla [diagnostica ACR](../container-registry/container-registry-diagnostics-audit-logs.md)
* Altre informazioni [sull'integrità di ACR](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/