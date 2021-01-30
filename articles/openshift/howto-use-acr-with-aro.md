---
title: Usare Container Registry di Azure con Azure Red Hat OpenShift
description: Informazioni su come eseguire il pull e l'esecuzione di un contenitore da Azure Container Registry nel cluster Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 91b43aa939579f5df8115048cf52b9bcdd7ade3d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063988"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Usare Container Registry di Azure con Azure Red Hat OpenShift (ARO)

Azure Container Registry (ACR) è un servizio Registro contenitori gestito che è possibile usare per archiviare le immagini del contenitore Docker privato con funzionalità aziendali come la replica geografica. Per accedere a ACR da un cluster ARO, il cluster può eseguire l'autenticazione con ACR archiviando le credenziali di accesso di Docker in un segreto Kubernetes.  Analogamente, un cluster ARO può usare un imagePullSecret nella specifica pod per eseguire l'autenticazione nel registro di sistema durante il pull dell'immagine. Questo articolo illustra come configurare un Container Registry di Azure con un cluster Azure Red Hat OpenShift per archiviare ed eseguire il pull delle immagini del contenitore Docker privato.

## <a name="prerequisites"></a>Prerequisiti

Questa guida presuppone che si disponga di un Container Registry di Azure esistente. In caso contrario, usare le istruzioni portale di Azure o dell'interfaccia della riga di comando di [Azure](../container-registry/container-registry-get-started-azure-cli.md) per creare un registro contenitori.

Questo articolo presuppone anche che sia presente un cluster Azure Red Hat OpenShift esistente e che sia installata l'interfaccia della riga di comando `oc` . In caso contrario, seguire le istruzioni riportate nell' [esercitazione creare un cluster Aro](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Ottenere un segreto di pull

Per accedere al registro di sistema dal cluster ARO è necessario un segreto di pull da ACR.

Per ottenere le credenziali del segreto di pull, è possibile usare l'portale di Azure o l'interfaccia della riga di comando di Azure.

Se si usa il portale di Azure, passare all'istanza di ACR e selezionare **chiavi di accesso**.  Il `docker-username` è il nome del registro contenitori, usare password o password2 per `docker-password` .

![Chiavi di accesso](./media/acr-access-keys.png)

È invece possibile usare l'interfaccia della riga di comando di Azure per ottenere le credenziali seguenti:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Creare il segreto Kubernetes

A questo punto si useranno queste credenziali per creare un segreto Kubernetes. Eseguire il comando seguente con le credenziali di ACR:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Questo segreto verrà archiviato nel progetto OpenShift corrente (spazio dei nomi Kubernetes) e sarà possibile fare riferimento solo ai pod creati in tale progetto.  Per altre istruzioni sulla creazione di un segreto di pull a livello di cluster, vedere questo [documento](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) .

## <a name="create-a-pod-using-a-private-registry-image"></a>Creare un pod usando un'immagine del registro di sistema privata

Ora che il cluster ARO è stato connesso al registro contenitori di contenitori, è possibile eseguire il pull di un'immagine dall'ACR per creare un pod.

Iniziare con un podSpec e specificare il segreto creato come imagePullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Per verificare che il POD sia attivo e in esecuzione, eseguire questo comando e attendere che lo stato sia **in esecuzione**:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Passaggi successivi

* [Registro Azure Container](../container-registry/container-registry-concepts.md)
* [Avvio rapido: Creare un registro contenitori privato usando l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md)
