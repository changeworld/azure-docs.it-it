---
title: Avvio rapido - Creare un cluster del servizio Azure Kubernetes
description: Informazioni su come creare rapidamente un cluster Kubernetes tramite un modello di Azure Resource Manager e distribuire un'applicazione nel servizio Azure Kubernetes
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4bcaafdb1f465fb8568078dfb5bfaf988d0cd587
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764442"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes con un modello di Resource Manager

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Contenuto dell'avvio rapido:
* Distribuire un cluster del servizio Web Del Servizio Azure Resource Manager servizio Azure Resource Manager servizio. 
* Eseguire un'applicazione multi-contenitore con un front-end Web e un'istanza Redis nel cluster. 

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Questa guida introduttiva presuppone una comprensione di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.61 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

- Per creare un cluster del servizio AKS usando un Resource Manager, specificare una chiave pubblica SSH. Se è necessaria questa risorsa, vedere la sezione seguente. in caso contrario, passare [alla sezione Esaminare il](#review-the-template) modello.

### <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per accedere ai nodi del servizio AKS, connettersi usando una coppia di chiavi SSH (pubblica e privata), generata tramite il `ssh-keygen` comando . Per impostazione predefinita, questi file vengono creati nella directory *~/.ssh*. `ssh-keygen`L'esecuzione del comando sovrascriverà qualsiasi coppia di chiavi SSH con lo stesso nome già esistente nel percorso specificato.

1. Passare a [https://shell.azure.com](https://shell.azure.com) per aprire Cloud Shell nel browser.

1. Eseguire il comando `ssh-keygen`. L'esempio seguente crea una coppia di chiavi SSH usando la crittografia RSA e una lunghezza in bit pari a 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Per altre informazioni su come creare le chiavi SSH, vedere [Creare e gestire chiavi SSH per l'autenticazione in una macchina virtuale Linux in Azure][ssh-keys].

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Per altri esempi per il servizio Azure Kubernetes, vedere il sito dei [modelli di avvio rapido per il servizio Azure Kubernetes][aks-quickstart-templates].

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare il pulsante seguente per accedere ad Azure e aprire un modello.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

    Per questo modello di avvio rapido, lasciare i valori predefiniti per *Dimensioni disco sistema operativo*, *Numero di agenti*, *Dimensioni macchina virtuale agente*, *Tipo di sistema operativo* e *Versione di Kubernetes*. Specificare i valori personalizzati per i parametri del modello seguenti:

    * **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: Selezionare **Crea nuovo**. Specificare un nome univoco per il gruppo di risorse, ad esempio *myResourceGroup* e quindi scegliere **OK**.
    * **Località**: Selezionare una località, ad esempio **Stati Uniti orientali**.
    * **Nome cluster**: Specificare un nome univoco per il cluster del servizio Azure Kubernetes, ad esempio *myAKSCluster*.
    * **Prefisso DNS**: Specificare un prefisso DNS univoco per il cluster, ad esempio *myakscluster*.
    * **Nome utente amministratore Linux**: Specificare il nome utente per la connessione tramite SSH, ad esempio *azureuser*.
    * **Chiave pubblica RSA SSH**: Copiare e incollare la parte *pubblica* della coppia di chiavi SSH (per impostazione predefinita, il contenuto di *~/.ssh/id_rsa.pub*).

    ![Modello di Resource Manager per creare un cluster del servizio Azure Kubernetes nel portale](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selezionare **Rivedi e crea**.

La creazione del cluster del servizio Azure Kubernetes richiede alcuni minuti. Attendere il completamento della distribuzione del cluster prima di procedere al passaggio successivo.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

### <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare il client della riga di comando Kubernetes, [kubectl][kubectl]. `kubectl` è già installato se si usa Azure Cloud Shell. 

1. Eseguire `kubectl` l'installazione in locale [usando il comando az aks install-cli:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Configurare `kubectl` per connettersi al cluster Kubernetes usando il [comando az aks get-credentials.][az-aks-get-credentials] Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verificare la connessione al cluster usando il [comando kubectl get.][kubectl-get] Questo comando restituisce un elenco dei nodi del cluster.

    ```console
    kubectl get nodes
    ```

    L'output mostra i nodi creati nei passaggi precedenti. Assicurarsi che lo stato di tutti i nodi sia impostato su *Pronto*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Eseguire l'applicazione

Un [file manifesto Kubernetes][kubernetes-deployment] definisce lo stato desiderato di un cluster, ad esempio le immagini del contenitore da eseguire. 

In questa guida introduttiva si userà un manifesto per creare tutti gli oggetti necessari per eseguire [l'applicazione Azure Vote.][azure-vote-app] Questo manifesto include due [distribuzioni di Kubernetes:][kubernetes-deployment]
* Le applicazioni Python di Azure Vote di esempio.
* Istanza di Redis. 

Vengono [creati anche due servizi Kubernetes:][kubernetes-service]
* Un servizio interno per l'istanza di Redis.
* Un servizio esterno per accedere all'applicazione Azure Vote da Internet.

1. Creare un file denominato `azure-vote.yaml`.
    * Se si usa il Azure Cloud Shell, questo file può essere creato usando o come se si lavora `vi` in un sistema fisico o `nano` virtuale
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

    L'output mostra le distribuzioni e i servizi creati correttamente:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

### <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Monitorare lo stato usando [il comando kubectl get service][kubectl-get] con l'argomento `--watch` .

```console
kubectl get service azure-vote-front --watch
```

**L'output EXTERNAL-IP** per `azure-vote-front` il servizio verrà inizialmente visualizzato come in *sospeso.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando **l'indirizzo EXTERNAL-IP** cambia da *in sospeso* a un indirizzo IP pubblico effettivo, usare `CTRL-C` per arrestare il processo di `kubectl` controllo. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per vedere in azione l'app Azure Vote, aprire un Web browser all'indirizzo IP esterno del servizio.

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per evitare addebiti per Azure, pulire le risorse non necessarie. Usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].
> 
> Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="get-the-code"></a>Ottenere il codice

In questa guida introduttiva sono state usate immagini del contenitore preesiste per creare una distribuzione Kubernetes. Il codice dell'applicazione correlato, il dockerfile e il file manifesto Kubernetes sono [disponibili in GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Kubernetes in cui è stata quindi distribuita un'applicazione multi-contenitore. [Accedere al dashboard Web di Kubernetes per][kubernetes-dashboard] il cluster del servizio Kubernetes.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
