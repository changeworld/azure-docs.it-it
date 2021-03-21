---
title: 'Guida introduttiva: distribuire un cluster AKS usando il portale di Azure'
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione e monitorare le prestazioni nel servizio Azure Kubernetes usando il portale di Azure.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperfq3
ms.openlocfilehash: 4763e72e3a50bd6c84f158658b7531a25e4ceec9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492915"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes usando il portale di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Contenuto dell'avvio rapido:
* Distribuire un cluster AKS usando il portale di Azure. 
* Eseguire un'applicazione a più contenitori con un front-end Web e un'istanza di redis nel cluster. 
* Monitorare l'integrità del cluster e dei pod che eseguono l'applicazione.

![Immagine del passaggio all'applicazione Azure Vote di esempio](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Questa guida introduttiva presuppone una comprensione di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

1. Nel menu del portale di Azure o nella **home page** selezionare **Crea una risorsa**.

2. Selezionare **Contenitori** > **Servizio Kubernetes**.

3. Nella pagina **Informazioni di base** configurare le opzioni seguenti:
    - **Dettagli progetto**: 
        * Selezionare una **sottoscrizione** di Azure.
        * Selezionare o creare un **gruppo di risorse** di Azure, ad esempio *myResourceGroup*.
    - **Dettagli cluster**: 
        * Immettere un **nome cluster Kubernetes**, ad esempio *myAKSCluster*. 
        * Selezionare un'**area** e una **versione di Kubernetes** per il cluster del servizio Azure Kubernetes.
    - **Pool di nodi primario**: 
        * Selezionare il valore di **Dimensioni nodo** della macchina virtuale per i nodi del servizio Azure Kubernetes. Le dimensioni della macchina virtuale *non possono* essere modificate dopo che un cluster del servizio Azure Container è stato distribuito.
        * Selezionare il numero di nodi da distribuire nel cluster. Per questa guida introduttiva, impostare **Numero di nodi** su *1*. Il numero di nodi *può* essere modificato dopo che il cluster è stato distribuito.
    
    ![Creare un cluster del servizio Azure Kubernetes - fornire informazioni di base](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Selezionare **Avanti: Pool di nodi** al termine.

5. Mantieni le opzioni predefinite dei **pool di nodi** . Nella parte inferiore della schermata fare clic su **Successivo: Autenticazione**.
    > [!CAUTION]
    > Le entità servizio Azure AD appena create possono richiedere alcuni minuti per propagarsi e diventare disponibili, causando errori di convalida e di "entità servizio non trovata" nel portale di Azure. Se si raggiunge questo problema, vedere [l'articolo sulla risoluzione dei problemi](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) per la mitigazione.

6. Nella pagina **Autenticazione** configurare le opzioni seguenti:
    - Creare una nuova identità del cluster in uno dei seguenti casi:
        * Lasciando il campo di **autenticazione** con l' **identità gestita System-Assinged** o
        * Scelta dell' **entità servizio** per l'utilizzo di un'entità servizio. 
            * Selezionare *(nuovo) entità servizio predefinita* per creare un'entità servizio predefinita oppure
            * Selezionare *Configura entità servizio* per usare una esistente. È necessario specificare l'ID e il segreto del client SPN dell'entità esistente.
    - Abilitare l'opzione di controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) per fornire un controllo più accurato sull'accesso alle risorse Kubernetes distribuite nel cluster AKS.

    Per impostazione predefinita, viene usata la rete di livello *Basic* e Monitoraggio di Azure per i contenitori è abilitato. 

7. Una volta completata la convalida, selezionare **Rivedi e crea** e quindi **Crea**. 


8. La creazione del cluster del servizio Azure Kubernetes richiede alcuni minuti. Al termine della distribuzione, passare alla risorsa eseguendo una delle operazioni seguenti:
    * Fare clic su **Vai alla risorsa** o
    * Passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS. 
        * Per il dashboard del cluster di esempio seguente: esplorazione di *myResourceGroup* e selezione della risorsa *myAKSCluster* .

        ![Esempio di dashboard del servizio Azure Kubernetes nel portale di Azure](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare il client da riga di comando Kubernetes, [kubectl][kubectl]. `kubectl` è già installato se si usa Azure Cloud Shell. 

1. Aprire Cloud Shell usando il pulsante `>_` nella parte superiore del portale di Azure.

    ![Aprire Azure Cloud Shell nel portale](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Per eseguire queste operazioni in un'installazione della shell locale:
    > 1. Verificare l'installazione dell'interfaccia della riga di comando di Azure.
    > 2. Connettersi ad Azure tramite il `az login` comando.

2. Configurare `kubectl` per connettersi al cluster Kubernetes usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] . Il comando seguente Scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verificare la connessione al cluster usando `kubectl get` per restituire un elenco dei nodi del cluster.

    ```console
    kubectl get nodes
    ```

    Output Mostra il singolo nodo creato nei passaggi precedenti. Verificare che lo stato del nodo sia *pronto*:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato del cluster, ad esempio le immagini del contenitore da eseguire. 

In questa Guida introduttiva si userà un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione Azure vote. Questo manifesto include due distribuzioni Kubernetes:
* Applicazioni Python di Azure vote di esempio.
* Istanza di Redis. 

Vengono creati anche due servizi Kubernetes:
* Servizio interno per l'istanza di Redis.
* Un servizio esterno per accedere all'applicazione Azure vote da Internet.

1. Nella Cloud Shell usare un editor per creare un file denominato `azure-vote.yaml` , ad esempio:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml` o 
    * `vi azure-vote.yaml`. 

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

1. Distribuire l'applicazione usando il `kubectl apply` comando e specificare il nome del manifesto YAML:

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

Per monitorare lo stato di avanzamento, utilizzare il `kubectl get service` comando con l' `--watch` argomento.

```console
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

![Immagine del passaggio all'applicazione Azure Vote di esempio](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitoraggio di stato e log

Dopo la creazione del cluster, Monitoraggio di Azure per i contenitori è stato abilitato. Il monitoraggio di Azure per i contenitori fornisce le metriche di integrità sia per il cluster AKS che per i pod in esecuzione nel cluster.

Il popolamento dei dati delle metriche richiede alcuni minuti nell'portale di Azure. Per visualizzare lo stato di integrità corrente, il tempo di esecuzione e l'utilizzo delle risorse per i pod di voto di Azure:

1. Tornare alla risorsa AKS nella portale di Azure.
1. In **monitoraggio** sul lato sinistro scegliere **Insights (informazioni dettagliate**).
1. In alto, scegliere **+ Aggiungi filtro**.
1. Selezionare **spazio dei nomi** come proprietà, quindi scegliere *\<All but kube-system\>* .
1. Selezionare i **contenitori** per visualizzarli.

`azure-vote-back` `azure-vote-front` Vengono visualizzati i contenitori e, come illustrato nell'esempio seguente:

![Visualizzare l'integrità dei contenitori in esecuzione nel servizio Azure Kubernetes](media/kubernetes-walkthrough-portal/monitor-containers.png)

Per visualizzare i log per il `azure-vote-front` Pod, selezionare **Visualizza log del contenitore** nell'elenco a discesa contenitori. Questi log includono i flussi *stdout* e *stderr* del contenitore.

![Visualizzare i log dei contenitori nel servizio Azure Kubernetes](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Eliminare il cluster

Per evitare addebiti per Azure, pulire le risorse non necessarie. Selezionare il pulsante **Elimina** nel dashboard del cluster AKS. È anche possibile usare il comando [AZ AKS Delete][az-aks-delete] nel cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].
> 
> Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="get-the-code"></a>Ottenere il codice

Le immagini del contenitore preesistenti sono state usate in questa Guida introduttiva per creare una distribuzione di Kubernetes. Il codice dell'applicazione correlato, Dockerfile, e il file manifesto Kubernetes sono [disponibili in GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato distribuito un cluster Kubernetes e quindi è stata distribuita un'applicazione a più contenitori. Accedere al dashboard Web di Kubernetes per il cluster AKS.


Per altre informazioni su AKS, vedere un esempio completo, inclusa la compilazione di un'applicazione, la distribuzione da Container Registry di Azure, l'aggiornamento di un'applicazione in esecuzione e il ridimensionamento e l'aggiornamento del cluster, continuare con l'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations