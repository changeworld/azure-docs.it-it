---
title: 'Guida introduttiva: Distribuire un cluster del servizio Web Disassoce con il portale di Azure'
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione e monitorare le prestazioni nel servizio Azure Kubernetes usando il portale di Azure.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779706"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes usando il portale di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Contenuto dell'avvio rapido:
* Distribuire un cluster del servizio Web Diaks usando il portale di Azure. 
* Eseguire un'applicazione multi-contenitore con un front-end Web e un'istanza di Redis nel cluster. 
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
    - **Dettagli del progetto:** 
        * Selezionare una **sottoscrizione** di Azure.
        * Selezionare o creare un gruppo **di risorse di** Azure, ad esempio *myResourceGroup.*
    - **Dettagli del cluster:** 
        * Immettere un **nome cluster Kubernetes**, ad esempio *myAKSCluster*. 
        * Selezionare un'**area** e una **versione di Kubernetes** per il cluster del servizio Azure Kubernetes.
    - **Pool di nodi primario**: 
        * Selezionare il valore di **Dimensioni nodo** della macchina virtuale per i nodi del servizio Azure Kubernetes. Le dimensioni della macchina virtuale *non possono* essere modificate dopo che un cluster del servizio Azure Container è stato distribuito.
        * Selezionare il numero di nodi da distribuire nel cluster. Per questa guida introduttiva, impostare **Numero di nodi** su *1*. Il numero di nodi *può* essere modificato dopo che il cluster è stato distribuito.
    
    ![Creare un cluster del servizio Azure Kubernetes - fornire informazioni di base](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Selezionare **Avanti: Pool di nodi** al termine.

5. Mantenere le opzioni **predefinite pool di** nodi. Nella parte inferiore della schermata fare clic su **Successivo: Autenticazione**.
    > [!CAUTION]
    > La Azure AD propagazione e la disponibilità delle entità servizio appena create possono richiedere alcuni minuti, causando errori di "entità servizio non trovata" ed errori di convalida in portale di Azure. Se si verifica questo problema, vedere [l'articolo sulla risoluzione dei problemi per](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) la mitigazione.

6. Nella pagina **Autenticazione** configurare le opzioni seguenti:
    - Creare una nuova identità del cluster:
        * Lasciare il **campo** Autenticazione **con l'identità gestita con sistema** oppure
        * Scelta **dell'entità servizio** per usare un'entità servizio. 
            * Selezionare *(nuova) entità servizio predefinita* per creare un'entità servizio predefinita oppure
            * Selezionare *Configura entità servizio* per usarne una esistente. Sarà necessario fornire l'ID client e il segreto SPN dell'entità esistente.
    - Abilitare l'opzione Controllo degli accessi in base al ruolo di Kubernetes per fornire un controllo più granulare sull'accesso alle risorse Kubernetes distribuite nel cluster del servizio Kubernetes.

    Per impostazione predefinita, viene usata la rete di livello *Basic* e Monitoraggio di Azure per i contenitori è abilitato. 

7. Una volta completata la convalida, selezionare **Rivedi e crea** e quindi **Crea**. 


8. La creazione del cluster del servizio Azure Kubernetes richiede alcuni minuti. Al termine della distribuzione, passare alla risorsa:
    * Fare **clic su Vai alla risorsa** oppure
    * Selezionare il gruppo di risorse del cluster del servizio Web Diacks e selezionare la risorsa del servizio. 
        * Per il dashboard del cluster di esempio seguente: cercare *myResourceGroup* e selezionare *la risorsa myAKSCluster.*

        ![Esempio di dashboard del servizio Azure Kubernetes nel portale di Azure](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare il client della riga di comando kubernetes, [kubectl][kubectl]. `kubectl` è già installato se si usa Azure Cloud Shell. 

1. Aprire Cloud Shell usando il pulsante `>_` nella parte superiore del portale di Azure.

    ![Aprire Azure Cloud Shell nel portale](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Per eseguire queste operazioni in un'installazione della shell locale:
    > 1. Verificare che l'interfaccia della riga di comando di Azure sia installata.
    > 2. Connettersi ad Azure tramite il `az login` comando .

2. Configurare `kubectl` per connettersi al cluster Kubernetes usando il [comando az servizio Kubernetes get-credentials.][az-aks-get-credentials] Il comando seguente scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verificare la connessione al cluster usando `kubectl get` per restituire un elenco dei nodi del cluster.

    ```console
    kubectl get nodes
    ```

    L'output mostra il singolo nodo creato nei passaggi precedenti. Verificare che lo stato del nodo sia *Pronto:*

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto Kubernetes definisce lo stato desiderato di un cluster, ad esempio le immagini del contenitore da eseguire. 

In questa guida introduttiva si userà un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione Azure Vote. Questo manifesto include due distribuzioni kubernetes:
* Applicazioni Python di azure vote di esempio.
* Un'istanza di Redis. 

Vengono creati anche due servizi Kubernetes:
* Un servizio interno per l'istanza di Redis.
* Un servizio esterno per accedere all'applicazione Azure Vote da Internet.

1. Nel Cloud Shell usare un editor per creare un file denominato `azure-vote.yaml` , ad esempio:
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

1. Distribuire l'applicazione usando `kubectl apply` il comando e specificare il nome del manifesto YAML:

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

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato di avanzamento, `kubectl get service` usare il comando con l'argomento `--watch` .

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

![Immagine del passaggio all'applicazione Azure Vote di esempio](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitoraggio di stato e log

Dopo la creazione del cluster, Monitoraggio di Azure per i contenitori è stato abilitato. Monitoraggio di Azure per i contenitori fornisce metriche di integrità per il cluster del servizio Contenitore di Microsoft Monitoraggio di Azure e i pod in esecuzione nel cluster.

Il popolamento dei dati delle metriche nel portale di Azure. Per visualizzare lo stato di integrità corrente, il tempo di attività e l'utilizzo delle risorse per i pod di Azure Vote:

1. Tornare alla risorsa AKS nel portale di Azure.
1. In **Monitoraggio** sul lato sinistro scegliere **Informazioni dettagliate.**
1. Nella parte superiore scegliere **+ Aggiungi filtro.**
1. Selezionare **Spazio** dei nomi come proprietà e quindi scegliere *\<All but kube-system\>* .
1. Selezionare **Contenitori** per visualizzarli.

Verranno `azure-vote-back` visualizzati `azure-vote-front` i contenitori e , come illustrato nell'esempio seguente:

![Visualizzare l'integrità dei contenitori in esecuzione nel servizio Azure Kubernetes](media/kubernetes-walkthrough-portal/monitor-containers.png)

Per visualizzare i log per il `azure-vote-front` pod, selezionare **Visualizza log contenitori** dall'elenco a discesa dei contenitori. Questi log includono i flussi *stdout* e *stderr* del contenitore.

![Visualizzare i log dei contenitori nel servizio Azure Kubernetes](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Eliminare il cluster

Per evitare addebiti per Azure, pulire le risorse non necessarie. Selezionare il pulsante **Elimina nel** dashboard del cluster del servizio Web Diaks. È anche possibile usare il [comando az aks delete][az-aks-delete] nel Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].
> 
> Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="get-the-code"></a>Ottenere il codice

In questa guida introduttiva sono state usate immagini del contenitore preesiste per creare una distribuzione Kubernetes. Il codice dell'applicazione correlato, il file manifesto Dockerfile e Kubernetes sono [disponibili in GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Kubernetes e quindi è stata distribuita un'applicazione multi-contenitore. Accedere al dashboard Web Di Kubernetes per il cluster del servizio Kubernetes.


Per altre informazioni sul servizio Kubernetes, vedere un esempio completo, tra cui la compilazione di un'applicazione, la distribuzione da Registro Azure Container, l'aggiornamento di un'applicazione in esecuzione e il ridimensionamento e l'aggiornamento del cluster, continuare con l'esercitazione sul cluster Kubernetes.

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations