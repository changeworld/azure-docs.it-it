---
title: Creare un contenitore Windows Server in un cluster del servizio Azure Container usando l'interfaccia della riga di comando di Azure
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione in un contenitore Windows Server in servizio Azure Kubernetes (AKS) usando l'interfaccia della riga di comando di Azure.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 617590a3f482e246b8af5db6dd906591c16b20fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769428"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Creare un contenitore Windows Server in un cluster servizio Azure Kubernetes (AKS) usando l'interfaccia della riga di comando di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. In questo articolo viene distribuito un cluster del servizio AzureKs usando l'interfaccia della riga di comando di Azure. È anche possibile distribuire ASP.NET'applicazione di esempio in un contenitore di Windows Server nel cluster.

![Immagine del passaggio all'applicazione ASP.NET di esempio](media/windows-container/asp-net-sample-app.png)

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano pool di nodi multipli, si applicano le limitazioni seguenti:

* Non è possibile eliminare il primo pool di nodi.

Ai pool di nodi di Windows Server si applicano le limitazioni aggiuntive seguenti:

* Il cluster del servizio Azure Kubernetes può avere un massimo di 10 pool di nodi.
* Il cluster del servizio Azure Kubernetes può avere un massimo di 100 nodi in ogni pool di nodi.
* Il nome del pool di nodi di Windows Server ha un limite di 6 caratteri.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il comando [az group create][az-group-create].

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

> [!NOTE]
> Questo articolo usa la sintassi Bash per i comandi di questa esercitazione.
> Se si usa Azure Cloud Shell, assicurarsi che l'elenco a discesa in alto a sinistra nella finestra Cloud Shell sia impostato su **Bash.**

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Per eseguire un cluster del servizio Azure Kubernetes che supporti i pool di nodi per i contenitori di Windows Server, il cluster deve usare un criterio di rete che usi il plug-in di rete [Azure CNI][azure-cni-about] (avanzato). Per informazioni più dettagliate sulla pianificazione degli intervalli di subnet richiesti e sulle considerazioni sulla rete, vedere [Configurare le funzionalità di rete dell'interfaccia di rete dei contenitori di Azure][use-advanced-networking]. Usare il [comando az aks create per][az-aks-create] creare un cluster del servizio Web Diaz denominato *myAKSCluster.* Questo comando creerà le risorse di rete necessarie se non esistono.

* Il cluster è configurato con due nodi.
* I `--windows-admin-password` parametri e impostano le credenziali di amministratore per tutti i contenitori di Windows Server creati nel cluster e `--windows-admin-username` devono soddisfare i requisiti delle password di Windows [Server.][windows-server-password] Se non si specifica il *parametro windows-admin-password,* verrà richiesto di specificare un valore.
* Il pool di nodi usa `VirtualMachineScaleSets` .

> [!NOTE]
> Per garantire che il cluster funzioni in modo affidabile, è necessario eseguire almeno 2 (due) nodi nel pool di nodi predefinito.

Creare un nome utente da usare come credenziali di amministratore per i contenitori di Windows Server nel cluster. I comandi seguenti consentono di richiedere un nome utente e di impostarlo WINDOWS_USERNAME per l'uso in un comando successivo(tenere presente che i comandi in questo articolo vengono immessi in una shell BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Creare il cluster assicurandosi di specificare il `--windows-admin-username` parametro . Il comando di esempio seguente crea un cluster usando il *valore WINDOWS_USERNAME* impostato nel comando precedente. In alternativa, è possibile specificare un nome utente diverso direttamente nel parametro anziché usare *WINDOWS_USERNAME*. Il comando seguente richiede anche di creare una password per le credenziali di amministratore per i contenitori di Windows Server nel cluster. In alternativa, è possibile usare il *parametro windows-admin-password* e specificare il proprio valore.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Se viene visualizzato un errore di convalida della password, verificare che la password impostata soddisfi i requisiti delle [password di Windows Server.][windows-server-password] Se la password soddisfa i requisiti, provare a creare il gruppo di risorse in un'altra area. Provare quindi a creare il cluster con il nuovo gruppo di risorse.

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Occasionalmente, il provisioning del cluster può richiedere più tempo. In questi casi, attendere al massimo 10 minuti.

## <a name="add-a-windows-server-node-pool"></a>Aggiungere un pool di nodi di Windows Server

Per impostazione predefinita, un cluster del servizio Azure Kubernetes viene creato con un pool di nodi in grado di eseguire contenitori Linux. Usare `az aks nodepool add` il comando per aggiungere un pool di nodi aggiuntivo in grado di eseguire contenitori di Windows Server insieme al pool di nodi Linux.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Il comando precedente crea un nuovo pool di nodi denominato *npwin* e lo aggiunge al *myAKSCluster*. Quando si crea un pool di nodi per eseguire contenitori di Windows Server, il valore predefinito per *node-vm-size* è *Standard_D2s_v3*. Se si sceglie di impostare il *parametro node-vm-size,* controllare l'elenco delle [dimensioni delle macchine virtuali con restrizioni][restricted-vm-sizes]. Le dimensioni minime consigliate sono *Standard_D2s_v3*. Il comando precedente usa anche la subnet predefinita nella rete virtuale predefinita creata durante l'esecuzione di `az aks create` .

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```console
kubectl get nodes
```

L'output di esempio seguente mostra tutti i nodi nel cluster. Assicurarsi che lo stato di tutti i nodi sia impostato su *Pronto*:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. In questo articolo viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione di esempio ASP.NET in un contenitore di Windows Server. Questo manifesto include una [distribuzione di Kubernetes][kubernetes-deployment] per l'applicazione di esempio ASP.NET e un [servizio Kubernetes][kubernetes-service] esterno per accedere all'applicazione da Internet.

L'applicazione di esempio ASP.NET viene fornita nel quadro degli [esempi di .NET Framework][dotnet-samples] ed eseguita in un contenitore di Windows Server. Il servizio Azure Kubernetes prevede che i contenitori di Windows Server siano basati su immagini di *Windows Server 2019* o versione successiva. Il file manifesto di Kubernetes deve anche definire un [selettore di nodo][node-selector] per indicare al cluster del servizio Azure Kubernetes di eseguire il pod dell'applicazione di esempio ASP.NET in un nodo che sia in grado di eseguire contenitori di Windows Server.

Creare un file denominato `sample.yaml` e copiarlo nella definizione YAML seguente. Se si usa Azure Cloud Shell, questo file può essere creato usando `vi` o `nano` come se si usasse un sistema virtuale o fisico:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Distribuire l'applicazione usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f sample.yaml
```

L'output di esempio seguente mostra la distribuzione e il servizio creati correttamente:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. Occasionalmente, il provisioning del servizio può richiedere più tempo. In questi casi, attendere al massimo 10 minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```console
kubectl get service sample --watch
```

*EXTERNAL-IP* per il servizio *di esempio* viene inizialmente visualizzato come *pending*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per vedere in azione l'app di esempio, aprire un Web browser all'indirizzo IP esterno del servizio.

![Immagine del passaggio all'applicazione ASP.NET di esempio](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Se si riceve un timeout di connessione quando si tenta di caricare la pagina, è necessario verificare che l'app di esempio sia pronta con il comando seguente [kubectl get pods --watch]. In alcuni casi, il contenitore di Windows non verrà avviato al momento in cui è disponibile l'indirizzo IP esterno.

## <a name="delete-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete]. Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un cluster Kubernetes ASP.NET un'applicazione di esempio in un contenitore Windows Server. [Accesso al dashboard Web di Kubernetes][kubernetes-dashboard] per il cluster appena creato.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference