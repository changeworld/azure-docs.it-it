---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster servizio Azure Kubernetes (AKS) per ottenere le funzionalità e gli aggiornamenti della sicurezza più recenti.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779616"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Parte del ciclo di vita del cluster del servizio Kubernetes prevede l'esecuzione di aggiornamenti periodici alla versione più recente di Kubernetes. È importante applicare le versioni di sicurezza più recenti o eseguire l'aggiornamento per ottenere le funzionalità più recenti. Questo articolo illustra come aggiornare i componenti master o un singolo pool di nodi predefinito in un cluster del servizio AKS.

Per i cluster del servizio AKS che usano più pool di nodi o nodi di Windows Server, vedere [Aggiornare un pool di nodi nel servizio AKS.][nodepool-upgrade]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!WARNING]
> L'aggiornamento di un cluster del servizio Web Del servizio App attiva un cordone e svuota i nodi. Se è disponibile una quota di calcolo bassa, l'aggiornamento potrebbe non riuscire. Per altre informazioni, vedere [Aumentare le quote](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per verificare quali versioni di Kubernetes sono disponibili per il cluster, usare il comando [az aks get-upgrades][az-aks-get-upgrades]. Nell'esempio seguente viene verificata la presenza di aggiornamenti disponibili a *myAKSCluster* in *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster del servizio Kubernetes supportato, le versioni secondarie di Kubernetes non possono essere ignorate. Tutti gli aggiornamenti devono essere eseguiti in sequenza in base al numero di versione principale. Ad esempio, sono consentiti aggiornamenti tra *1.14.x*  ->  *1.15.x* o *1.15.x*  ->  *1.16.x,* ma non è consentito *1.14.x*  ->  *1.16.x.* 
> > È possibile ignorare più versioni solo quando si esegue l'aggiornamento da _una versione_ non supportata a una _versione supportata._ Ad esempio, è possibile completare un aggiornamento da una versione *1.10.x* non supportata > una *versione 1.15.x* supportata.

L'output di esempio seguente mostra che il cluster può essere aggiornato alle *versioni 1.19.1* e *1.19.3:*

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Se non è disponibile alcun aggiornamento, verrà visualizzato il messaggio:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Personalizzare l'aggiornamento dell'aumento del numero di nodi

> [!Important]
> I picchi di nodo richiedono la quota di sottoscrizione per il numero massimo di picchi richiesto per ogni operazione di aggiornamento. Ad esempio, un cluster con 5 pool di nodi, ognuno con un conteggio di 4 nodi, ha un totale di 20 nodi. Se ogni pool di nodi ha un valore di picco massimo del 50%, per completare l'aggiornamento sono necessarie risorse di calcolo aggiuntive e una quota IP di 10 nodi (2 nodi * 5 pool).
>
> Se si Azure CNI, verificare che siano disponibili indirizzi IP nella subnet e che soddisfino i requisiti [IP Azure CNI](configure-azure-cni.md).

Per impostazione predefinita, il servizio AKS configura gli aggiornamenti in modo che si sovrastrino con un nodo aggiuntivo. Un valore predefinito pari a uno per le impostazioni relative al picco massimo consentirà al servizio Servizio Di controllo di accesso di ridurre al minimo l'interruzione del carico di lavoro creando un nodo aggiuntivo prima del cordon/svuotamento delle applicazioni esistenti per sostituire un nodo con versione precedente. Il valore massimo di picco può essere personalizzato per ogni pool di nodi per consentire un compromesso tra la velocità di aggiornamento e l'interruzione dell'aggiornamento. Aumentando il valore di picco massimo, il processo di aggiornamento viene completato più velocemente, ma l'impostazione di un valore elevato per il picco massimo può causare interruzioni durante il processo di aggiornamento. 

Ad esempio, un valore di picco massimo del 100% offre il processo di aggiornamento più veloce possibile (raddoppiando il numero di nodi), ma causa anche lo svuotamento simultaneo di tutti i nodi nel pool di nodi. È possibile usare un valore più elevato, ad esempio questo, per gli ambienti di test. Per i pool di nodi di produzione, è consigliabile max_surge'impostazione predefinita del 33%.

AKS accetta sia valori interi che un valore percentuale per il picco massimo. Un numero intero, ad esempio "5", indica cinque nodi aggiuntivi di cui eseguire l'aumento. Il valore "50%" indica un valore di picco pari a metà del numero di nodi corrente nel pool. I valori massimi della percentuale di picco possono essere un minimo dell'1% e un massimo del 100%. Un valore percentuale viene arrotondato per es per es. al numero di nodi più vicino. Se il valore di picco massimo è inferiore al numero di nodi corrente al momento dell'aggiornamento, il conteggio dei nodi corrente viene usato per il valore di picco massimo.

Durante un aggiornamento, il valore di picco massimo può essere almeno 1 e un valore massimo uguale al numero di nodi nel pool di nodi. È possibile impostare valori maggiori, ma il numero massimo di nodi usati per il picco massimo non sarà superiore al numero di nodi nel pool al momento dell'aggiornamento.

> [!Important]
> L'impostazione del picco massimo in un pool di nodi è permanente.  Gli aggiornamenti successivi di Kubernetes o della versione del nodo useranno questa impostazione. È possibile modificare il valore di picco massimo per i pool di nodi in qualsiasi momento. Per i pool di nodi di produzione, è consigliabile un'impostazione max-surge del 33%.

Usare i comandi seguenti per impostare i valori massimi di sovratensione per i pool di nodi nuovi o esistenti.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco di versioni disponibili per il cluster del servizio Azure Kubernetes, usare il comando [az aks upgrade][az-aks-upgrade] per eseguire l'aggiornamento. Durante il processo di aggiornamento, il servizio AKS: 
- aggiungere un nuovo nodo del buffer (o un numero di nodi configurato nel [picco](#customize-node-surge-upgrade)massimo) al cluster che esegue la versione di Kubernetes specificata. 
- [Eseguire il cordon][kubernetes-drain] e svuotare uno dei nodi vecchi per ridurre al [][kubernetes-drain] minimo l'interruzione delle applicazioni in esecuzione. Se si usa il picco massimo, il numero di nodi viene svuotato e svuotato contemporaneamente al numero di nodi del buffer specificato. 
- Quando il nodo precedente è completamente svuotato, verrà ricreata l'immagine per ricevere la nuova versione e diventerà il nodo del buffer per il nodo seguente da aggiornare. 
- Questo processo si ripete fino a quando tutti i nodi del cluster non sono stati aggiornati. 
- Al termine del processo, l'ultimo nodo del buffer verrà eliminato, mantenendo il numero di nodi dell'agente esistente e il bilanciamento della zona.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone.

> [!IMPORTANT]
> Assicurarsi che qualsiasi replica (PDB) consenta lo spostamento di almeno 1 replica di pod alla volta; in caso contrario, l'operazione `PodDisruptionBudgets` di svuotamento/eliminazione avrà esito negativo.
> Se l'operazione di svuotamento ha esito negativo, l'operazione di aggiornamento avrà esito negativo per impostazione predefinita per garantire che le applicazioni non siano interrotte. Correggere il problema che ha causato l'arresto dell'operazione (PDB non corretti, mancanza di quota e così via) e riprovare.

Per verificare che l'aggiornamento sia stato completato correttamente, usare il comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che il cluster esegue *ora la versione 1.18.10:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Impostare il canale di aggiornamento automatico

Oltre ad aggiornare manualmente un cluster, è possibile impostare un canale di aggiornamento automatico nel cluster. Sono disponibili i canali di aggiornamento seguenti:

|Channel| Azione | Esempio
|---|---|---|
| `none`| disabilita gli aggiornamenti automatici e mantiene il cluster alla versione corrente di Kubernetes| Impostazione predefinita se lasciata invariata|
| `patch`| aggiornare automaticamente il cluster alla versione più recente della patch supportata quando diventa disponibile mantenendo la versione secondaria uguale.| Ad esempio, se un cluster esegue la *versione 1.17.7* e le versioni *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* sono disponibili, il cluster viene aggiornato alla *versione 1.17.9*|
| `stable`| aggiornare automaticamente il cluster alla versione della patch supportata più recente nella versione secondaria *N-1,* dove *N* è la versione secondaria supportata più recente.| Ad esempio, se un cluster esegue la *versione 1.17.7* e le versioni *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* sono disponibili, il cluster viene aggiornato alla *versione 1.18.6*.
| `rapid`| aggiornare automaticamente il cluster all'ultima versione di patch supportata nell'ultima versione secondaria supportata.| Nei casi in cui il cluster si trova in una versione di Kubernetes che si trova in una versione secondaria *N-2* in cui *N* è la versione secondaria supportata più recente, il cluster esegue prima l'aggiornamento alla versione della patch supportata più recente nella versione secondaria *N-1.* Ad esempio, se un cluster esegue la *versione 1.17.7* e le versioni *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* sono disponibili, il cluster viene prima aggiornato alla *versione 1.18.6* e quindi viene aggiornato alla *versione 1.19.1*.

> [!NOTE]
> L'aggiornamento automatico del cluster viene aggiornato solo alle versioni ga di Kubernetes e non viene aggiornato alle versioni di anteprima.

L'aggiornamento automatico di un cluster segue lo stesso processo dell'aggiornamento manuale di un cluster. Per altri dettagli, vedere Aggiornare [un cluster del servizio Web Diaks.][upgrade-cluster]

L'aggiornamento automatico del cluster per i cluster del servizio Web Disassociato di Microsoft è una funzionalità di anteprima.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registrare `AutoUpgradePreview` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando [il comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Per impostare il canale di aggiornamento automatico durante la creazione di un cluster, usare il *parametro auto-upgrade-channel,* simile all'esempio seguente.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Per impostare il canale di aggiornamento automatico nel cluster esistente, aggiornare il *parametro auto-upgrade-channel,* come nell'esempio seguente.

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
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
