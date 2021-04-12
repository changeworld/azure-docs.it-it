---
title: "Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione e monitorare le prestazioni nel servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: 8adfd1a6e26a3381653ca9a794b124e201b9d481
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106705"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Guida introduttiva: Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Contenuto dell'avvio rapido:
* Distribuire un cluster AKS usando l'interfaccia della riga di comando di Azure. 
* Eseguire un'applicazione a più contenitori con un front-end Web e un'istanza di redis nel cluster. 
* Monitorare l'integrità del cluster e dei pod che eseguono l'applicazione.

  ![App di voto distribuita nel servizio Azure Kubernetes](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Questa guida introduttiva presuppone una comprensione di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Per altre informazioni sulla creazione di un pool di nodi di Windows Server, vedere [Creare un cluster del servizio Azure Kubernetes che supporta i contenitori di Windows Server][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.64 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

> [!NOTE]
> Eseguire i comandi come amministratore se si prevede di eseguire i comandi in questa Guida introduttiva localmente anziché in Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un [gruppo di risorse](../azure-resource-manager/management/overview.md) di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, verrà richiesto di specificare un percorso. Questo percorso è: 
* Percorso di archiviazione dei metadati del gruppo di risorse.
* Dove le risorse vengono eseguite in Azure se non si specifica un'altra area durante la creazione della risorsa. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

Creare un gruppo di risorse con il comando [az group create][az-group-create].


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Output per il gruppo di risorse creato correttamente:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Abilita monitoraggio cluster

1. Verificare che *Microsoft. OperationsManagement* e *Microsoft. OperationalInsights* siano registrati nella sottoscrizione. Per verificare lo stato della registrazione:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Se non sono registrati, registrare *Microsoft. OperationsManagement* e *Microsoft. OperationalInsights* usando:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Abilitare [monitoraggio di Azure per i contenitori][azure-monitor-containers] usando il parametro *--Enable-addons Monitoring* . 

## <a name="create-aks-cluster"></a>Creare un cluster del servizio Azure Container

Creare un cluster AKS usando il comando [AZ AKS create][az-aks-create] . Nell'esempio seguente viene creato un cluster denominato *myAKSCluster* con un nodo: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster.

> [!NOTE]
> Quando si crea un cluster AKS, viene creato automaticamente un secondo gruppo di risorse per archiviare le risorse AKS. Per altre informazioni, vedere [Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare il client da riga di comando Kubernetes, [kubectl][kubectl]. `kubectl` è già installato se si usa Azure Cloud Shell. 

1. Installare `kubectl` localmente usando il comando [AZ AKS install-cli][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Configurare `kubectl` per connettersi al cluster Kubernetes usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] . Con il comando seguente:  
      * Scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle.
      * USA `~/.kube/config` , il percorso predefinito per il [file di configurazione Kubernetes][kubeconfig-file]. Specificare un percorso diverso per il file di configurazione Kubernetes usando *--file*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verificare la connessione al cluster usando il comando [kubectl Get][kubectl-get] . Questo comando restituisce un elenco dei nodi del cluster.

    ```azurecli-interactive
    kubectl get nodes
    ```

    Output Mostra il singolo nodo creato nei passaggi precedenti. Verificare che lo stato del nodo sia *pronto*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un [file manifesto di Kubernetes][kubernetes-deployment] definisce uno stato desiderato del cluster, ad esempio le immagini del contenitore da eseguire. 

In questa Guida introduttiva si userà un manifesto per creare tutti gli oggetti necessari per eseguire l' [applicazione Azure vote][azure-vote-app]. Questo manifesto include due [distribuzioni Kubernetes][kubernetes-deployment]:
* Applicazioni Python di Azure vote di esempio.
* Istanza di Redis. 

Vengono creati anche due [Servizi Kubernetes][kubernetes-service] :
* Servizio interno per l'istanza di Redis.
* Un servizio esterno per accedere all'applicazione Azure vote da Internet.

1. Creare un file denominato `azure-vote.yaml`.
    * Se si utilizza il Azure Cloud Shell, questo file può essere creato utilizzando `code` , `vi` o come se si utilizzasse `nano` un sistema fisico o virtuale
1. Copiare nella definizione YAML seguente:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Distribuire l'applicazione usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    L'output Mostra le distribuzioni e i servizi creati correttamente:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Monitorare lo stato di avanzamento usando il comando [kubectl Get Service][kubectl-get] con l' `--watch` argomento.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

L'output **IP esterno** per il `azure-vote-front` servizio viene inizialmente visualizzato come *in sospeso*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando l'indirizzo **IP esterno** passa da *in sospeso* a un indirizzo IP pubblico effettivo, usare `CTRL-C` per arrestare il processo di `kubectl` controllo. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per vedere in azione l'app Azure Vote, aprire un Web browser all'indirizzo IP esterno del servizio.

![App di voto distribuita nel servizio Azure Kubernetes](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Visualizzare le metriche di integrità dei nodi del cluster e dei Pod acquisiti da [monitoraggio di Azure per i contenitori][azure-monitor-containers] nel portale di Azure. 

## <a name="delete-the-cluster"></a>Eliminare il cluster

Per evitare addebiti per Azure, pulire le risorse non necessarie. Usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].
> 
> Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="get-the-code"></a>Ottenere il codice

Le immagini del contenitore preesistenti sono state usate in questa Guida introduttiva per creare una distribuzione di Kubernetes. Il codice dell'applicazione correlato, Dockerfile, e il file manifesto Kubernetes sono [disponibili in GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato distribuito un cluster Kubernetes e quindi è stata distribuita un'applicazione a più contenitori. [Accedere al dashboard Web di Kubernetes][kubernetes-dashboard] per il cluster AKS.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
