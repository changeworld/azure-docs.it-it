---
title: 'Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes usando PowerShell'
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione e monitorare le prestazioni nel servizio Azure Kubernetes tramite PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492898"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Guida introduttiva: Distribuire un cluster del servizio Azure Kubernetes tramite PowerShell

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Contenuto dell'avvio rapido:
* Distribuire un cluster AKS usando PowerShell. 
* Eseguire un'applicazione a più contenitori con un front-end Web e un'istanza di redis nel cluster. 
* Monitorare l'integrità del cluster e dei pod che eseguono l'applicazione.

Per altre informazioni sulla creazione di un pool di nodi di Windows Server, vedere [Creare un cluster del servizio Azure Kubernetes che supporta i contenitori di Windows Server][windows-container-powershell].

![App di voto distribuita nel servizio Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Questa guida introduttiva presuppone una comprensione di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si esegue PowerShell localmente, installare il modulo AZ PowerShell e connettersi al proprio account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si hanno più sottoscrizioni di Azure, selezionare l'ID sottoscrizione appropriato in cui verranno fatturate le risorse usando il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un [gruppo di risorse](../azure-resource-manager/management/overview.md) di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, verrà richiesto di specificare un percorso. Questo percorso è: 
* Percorso di archiviazione dei metadati del gruppo di risorse.
* Dove le risorse vengono eseguite in Azure se non si specifica un'altra area durante la creazione della risorsa. 

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroup** nell'area **eastus**.

Creare un gruppo di risorse con il cmdlet [New-AzResourceGroup][new-azresourcegroup].

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Output per il gruppo di risorse creato correttamente:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Creare un cluster del servizio Azure Container

1. Generare una coppia di chiavi SSH tramite l' `ssh-keygen` utilità da riga di comando. 
    * Per altre informazioni, vedere i [passaggi rapidi: Creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Creare un cluster AKS usando il cmdlet [New-AzAks][new-azaks] . Il monitoraggio di Azure per i contenitori è abilitato per impostazione predefinita.

    L'esempio seguente crea un cluster denominato **myAKSCluster** con un nodo. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni sul cluster.

> [!NOTE]
> Quando si crea un cluster AKS, viene creato automaticamente un secondo gruppo di risorse per archiviare le risorse AKS. Per altre informazioni, vedere [Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare il client da riga di comando Kubernetes, [kubectl][kubectl]. `kubectl` è già installato se si usa Azure Cloud Shell. 

1. Installare `kubectl` localmente utilizzando il `Install-AzAksKubectl` cmdlet:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Configurare `kubectl` per connettersi al cluster Kubernetes usando il cmdlet [Import-AzAksCredential][import-azakscredential] . Il cmdlet seguente Scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Verificare la connessione al cluster usando il comando [kubectl Get][kubectl-get] . Questo comando restituisce un elenco dei nodi del cluster.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    Output Mostra il singolo nodo creato nei passaggi precedenti. Verificare che lo stato del nodo sia *pronto*:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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
    * Se si utilizza il Azure Cloud Shell, questo file può essere creato utilizzando `vi` o `nano` come se si utilizzasse un sistema virtuale o fisico
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    L'output Mostra le distribuzioni e i servizi creati correttamente:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Monitorare lo stato di avanzamento usando il comando [kubectl Get Service][kubectl-get] con l' `--watch` argomento.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

L'output **IP esterno** per il `azure-vote-front` servizio viene inizialmente visualizzato come *in sospeso*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando l'indirizzo **IP esterno** passa da *in sospeso* a un indirizzo IP pubblico effettivo, usare `CTRL-C` per arrestare il processo di `kubectl` controllo. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per vedere in azione l'app Azure Vote, aprire un Web browser all'indirizzo IP esterno del servizio.

![App di voto distribuita nel servizio Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Visualizzare le metriche di integrità dei nodi del cluster e dei Pod acquisiti da monitoraggio di Azure per i contenitori nel portale di Azure. 

## <a name="delete-the-cluster"></a>Eliminare il cluster

Per evitare addebiti per Azure, pulire le risorse non necessarie. Usare il cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
