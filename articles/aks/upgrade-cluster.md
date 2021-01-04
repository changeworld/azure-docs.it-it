---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster di Azure Kubernetes Service (AKS) per ottenere le funzionalità più recenti e gli aggiornamenti della sicurezza.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 947d669d436308a550bce31f04c7b1a2b8a8485a
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734353"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Parte del ciclo di vita del cluster AKS comporta l'esecuzione di aggiornamenti periodici alla versione più recente di Kubernetes. È importante applicare le versioni di sicurezza più recenti o eseguire l'aggiornamento per ottenere le funzionalità più recenti. Questo articolo illustra come aggiornare i componenti Master o un singolo pool di nodi predefinito in un cluster AKS.

Per i cluster AKS che usano più pool di nodi o nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.65 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!WARNING]
> Un aggiornamento del cluster AKS attiva un cordone e lo svuotamento dei nodi. Se è disponibile una quota di calcolo bassa, l'aggiornamento potrebbe non riuscire. Per altre informazioni, vedere [aumentare le quote](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per verificare quali versioni di Kubernetes sono disponibili per il cluster, usare il comando [az aks get-upgrades][az-aks-get-upgrades]. Nell'esempio seguente viene verificata la disponibilità di aggiornamenti per *myAKSCluster* in *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster AKS supportato, non è possibile ignorare le versioni secondarie di Kubernetes. Tutti gli aggiornamenti devono essere eseguiti in sequenza in base al numero di versione principale. Ad esempio, gli aggiornamenti compresi tra *1.14. x*  ->  *1.15. x* o *1.15. x*  ->  *1.16. x* sono consentiti, tuttavia, *1.14*. x  ->  *1.16. x* non è consentito. 
> > È possibile ignorare più versioni solo quando si effettua l'aggiornamento da una _versione non supportata_ a una _versione supportata_. Ad esempio, un aggiornamento da un *1,10. x* non supportato--> un *1.15. x* supportato può essere completato.

L'output di esempio seguente mostra che il cluster può essere aggiornato alle versioni *1.19.1* e *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Se non è disponibile alcun aggiornamento, verrà ricevuto il messaggio:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Personalizzare l'aggiornamento del sovraccarico del nodo

> [!Important]
> I picchi di nodo richiedono una quota di sottoscrizione per il numero massimo di picchi richiesti per ogni operazione di aggiornamento. Ad esempio, un cluster con 5 pool di nodi, ognuno con un conteggio di 4 nodi, ha un totale di 20 nodi. Se ogni pool di nodi ha un valore di picco massimo del 50%, per completare l'aggiornamento è necessaria una quota di calcolo e di IP aggiuntiva di 10 nodi (2 nodi * 5 pool).
>
> Se si usa Azure CNI, verificare che siano presenti anche IP disponibili nella subnet per [soddisfare i requisiti IP di Azure CNI](configure-azure-cni.md).

Per impostazione predefinita, AKS configura gli aggiornamenti in modo che si picchino con un altro nodo. Il valore predefinito di uno per le impostazioni del picco massimo consentirà ad AKS di ridurre al minimo l'alterazione del carico di lavoro creando un nodo aggiuntivo prima del Cordon/svuotamento delle applicazioni esistenti per sostituire un nodo con versioni precedenti. Il valore max Surge può essere personalizzato per ogni pool di nodi per consentire un compromesso tra la velocità di aggiornamento e l'interruzione dell'aggiornamento. Aumentando il valore di aumento massimo, il processo di aggiornamento viene completato più velocemente, ma l'impostazione di un valore elevato per il picco massimo può causare interferenze durante il processo di aggiornamento. 

Ad esempio, un valore di picco massimo pari al 100% consente il processo di aggiornamento più rapido (raddoppiando il numero di nodi), ma comporta lo svuotamento simultaneo di tutti i nodi del pool di nodi. È possibile utilizzare un valore più elevato, ad esempio per gli ambienti di testing. Per i pool di nodi di produzione è consigliabile usare un'impostazione di max_surge pari al 33%.

AKS accetta sia valori integer che un valore percentuale per il numero massimo di picchi. Un numero intero, ad esempio "5", indica cinque nodi aggiuntivi per l'incremento. Il valore "50%" indica un valore di incremento della metà del numero di nodi corrente nel pool. Il numero massimo di valori percentuali di aumento può essere minimo dell'1% e un massimo del 100%. Un valore percentuale viene arrotondato per eccesso al numero di nodi più vicino. Se il valore max Surge è inferiore al numero di nodi corrente al momento dell'aggiornamento, il numero di nodi corrente viene usato per il valore max surge.

Durante un aggiornamento, il valore max Surge può essere un minimo di 1 e un valore massimo uguale al numero di nodi nel pool di nodi. È possibile impostare valori più grandi, ma il numero massimo di nodi usati per il picco massimo non sarà superiore al numero di nodi nel pool al momento dell'aggiornamento.

> [!Important]
> L'impostazione del picco massimo in un pool di nodi è permanente.  Gli aggiornamenti Kubernetes successivi o gli aggiornamenti della versione del nodo utilizzeranno questa impostazione. È possibile modificare il valore di aumento massimo per i pool di nodi in qualsiasi momento. Per i pool di nodi di produzione, è consigliabile usare un'impostazione di picco massimo del 33%.

Usare i comandi seguenti per impostare i valori di picco massimi per i pool di nodi nuovi o esistenti.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco di versioni disponibili per il cluster del servizio Azure Kubernetes, usare il comando [az aks upgrade][az-aks-upgrade] per eseguire l'aggiornamento. Durante il processo di aggiornamento, AKS effettuerà le seguenti operazioni: 
- aggiungere un nuovo nodo buffer (o tutti i nodi configurati in [picco massimo](#customize-node-surge-upgrade)) al cluster che esegue la versione specificata di Kubernetes. 
- [cordonare e svuotare][kubernetes-drain] uno dei nodi precedenti per ridurre al minimo le problematiche di esecuzione delle applicazioni. Se si usa il picco massimo, il numero di nodi [e lo svuotamento][kubernetes-drain] verranno interrotti contemporaneamente al numero di nodi del buffer specificati. 
- Quando il nodo precedente viene svuotato completamente, verrà ricreata l'immagine per ricevere la nuova versione e diventerà il nodo del buffer per il nodo seguente da aggiornare. 
- Questo processo si ripete fino a quando tutti i nodi del cluster non sono stati aggiornati. 
- Al termine del processo, l'ultimo nodo del buffer verrà eliminato, mantenendo il numero di nodi agente e il saldo della zona esistenti.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone.

> [!IMPORTANT]
> Assicurarsi che Any `PodDisruptionBudgets` (PDB) consenta lo spostamento di almeno una replica Pod alla volta, altrimenti l'operazione di svuotamento/rimozione avrà esito negativo.
> Se l'operazione di svuotamento non riesce, l'operazione di aggiornamento avrà esito negativo in base alla progettazione per assicurarsi che le applicazioni non vengano interrotte. Correggere il problema che ha causato l'arresto dell'operazione (PDB non corretto, mancanza di quota e così via) e ripetere l'operazione.

Per verificare che l'aggiornamento sia stato completato correttamente, usare il comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che il cluster ora esegue *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Imposta canale di aggiornamento automatico

Oltre a aggiornare manualmente un cluster, è possibile impostare un canale di aggiornamento automatico nel cluster. Sono disponibili i seguenti canali di aggiornamento:

|Canale| Azione | Esempio
|---|---|---|
| `none`| Disabilita gli aggiornamenti automatici e mantiene il cluster nella versione corrente di Kubernetes| Impostazione predefinita se lasciata invariata|
| `patch`| aggiornare automaticamente il cluster alla versione più recente della patch supportata quando diventa disponibile mantenendo la versione secondaria.| Ad esempio, se un cluster esegue la versione *1.17.7* e le versioni *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* sono disponibili, il cluster viene aggiornato a *1.17.9*|
| `stable`| aggiornare automaticamente il cluster alla versione di patch supportata più recente nella versione secondaria *n-1*, dove *n* è la versione secondaria supportata più recente.| Ad esempio, se un cluster esegue la versione *1.17.7* e le versioni *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* sono disponibili, il cluster viene aggiornato a *1.18.6*.
| `rapid`| aggiornare automaticamente il cluster alla versione più recente supportata della patch nella versione secondaria supportata più recente.| Nei casi in cui il cluster si trova in una versione di Kubernetes che si trova in una versione secondaria *n-2* in cui *n* è la versione secondaria supportata più recente, il cluster esegue prima di tutto l'aggiornamento alla versione più recente della patch supportata in una versione secondaria *n-1* . Ad esempio, se un cluster esegue la versione *1.17.7* e le versioni *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* sono disponibili, il cluster viene aggiornato per primo a *1.18.6*, quindi viene aggiornato a *1.19.1*.

> [!NOTE]
> Aggiornamento automatico del cluster solo aggiornamenti alle versioni GA di Kubernetes e non si aggiornerà con le versioni di anteprima.

L'aggiornamento automatico di un cluster segue lo stesso processo di aggiornamento manuale di un cluster. Per informazioni dettagliate, vedere [aggiornare un cluster AKS][upgrade-cluster].

L'aggiornamento automatico del cluster per i cluster AKS è una funzionalità in anteprima.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registrare il `AutoUpgradePreview` flag funzionalità usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Per impostare il canale di aggiornamento automatico durante la creazione di un cluster, usare il parametro del *canale di aggiornamento automatico* , in modo simile all'esempio seguente.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Per impostare il canale di aggiornamento automatico nel cluster esistente, aggiornare il parametro del *canale di aggiornamento automatico* , in modo simile all'esempio seguente.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare un cluster esistente del servizio Azure Kubernetes. Per altre informazioni sulla distribuzione e sulla gestione dei cluster del servizio Azure Kubernetes, vedere le relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni sul servizio Azure Kubernetes][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster