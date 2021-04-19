---
title: Creare un cluster privato del servizio Azure Kubernetes
description: Informazioni su come creare un cluster privato del servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 339bb41aed5ead3d7ee7d1217bfbc771cf068832
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719116"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Creare un cluster privato del servizio Azure Kubernetes

In un cluster privato, il piano di controllo o il server API ha indirizzi IP interni definiti nel documento [RFC1918 - Allocazione](https://tools.ietf.org/html/rfc1918) di indirizzi per Internet privato. Usando un cluster privato, è possibile garantire che il traffico di rete tra il server API e i pool di nodi rimanga solo nella rete privata.

Il piano di controllo o il server API si trova in una sottoscrizione di Azure gestita dal servizio Azure Kubernetes. Un cluster o un pool di nodi del cliente si trova nella sottoscrizione del cliente. Il server e il cluster o il pool di nodi possono comunicare tra loro tramite il [servizio di collegamento privato di Azure][private-link-service] nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster AKS del cliente.

## <a name="region-availability"></a>Aree di disponibilità

Il cluster privato è disponibile nelle aree pubbliche, nelle aree Azure per enti pubblici e nelle Azure China (21Vianet) in cui è supportato [il servizio Web Diaks.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

> [!NOTE]
> Azure per enti pubblici sono supportati, ma US Gov Texas non è attualmente supportato a causa del supporto per il collegamento privato mancante.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva
* Il servizio di collegamento privato è supportato solo su Azure Load Balancer standard. Azure Load Balancer di base non è supportato.  
* Per usare un server DNS personalizzato, aggiungere l'IP DNS di Azure 168.63.129.16 come server DNS upstream nel server DNS personalizzato.

## <a name="create-a-private-aks-cluster"></a>Creare un cluster AKS privato

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse o usarne uno esistente per il cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Funzionalità di rete di base predefinita 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Dove `--enable-private-cluster` è un flag obbligatorio per un cluster privato. 

### <a name="advanced-networking"></a>Rete avanzata  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Dove `--enable-private-cluster` è un flag obbligatorio per un cluster privato. 

> [!NOTE]
> Se il CIDR dell'indirizzo del bridge Docker (172.17.0.1/16) si scontra con il CIDR della subnet, modificare l'indirizzo del bridge Docker in modo appropriato.

## <a name="configure-private-dns-zone"></a>Configurare DNS privato zona 

È possibile usare i parametri seguenti per configurare DNS privato zona.

- "System" è il valore predefinito. Se l'argomento --private-dns-zone viene omesso, il servizio DNS privato AKS creerà una zona nel gruppo di risorse del nodo.
- "None" significa che il DNS privato AKS non creerà una zona.  A questo scopo, è necessario bring your own dns server e configurare la risoluzione DNS per il nome di dominio completo privato.  Se non si configura la risoluzione DNS, IL DNS è risolvibile solo all'interno dei nodi dell'agente e causerà problemi del cluster dopo la distribuzione. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" richiede la creazione di una zona DNS privato in questo formato per il cloud globale di Azure: `privatelink.<region>.azmk8s.io` . In futuro sarà necessario l'ID risorsa DNS privato zona.  Sarà inoltre necessaria un'identità assegnata dall'utente o un'entità servizio con almeno i `private dns zone contributor`  ruoli `vnet contributor` e .
- "fqdn-subdomain" può essere utilizzato con "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" solo per fornire funzionalità di sottodominio a `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Prerequisiti

* AKS Preview versione 0.5.7 o successiva
* L'API versione 2020-11-01 o successiva

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Creare un cluster del servizio Web Disassoce privato con DNS privato servizio

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Creare un cluster del servizio AKS privato con una zona DNS privato personalizzata

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Opzioni per la connessione al cluster privato

L'endpoint del server API non ha un indirizzo IP pubblico. Per gestire il server API, è necessario usare una macchina virtuale che abbia accesso alla rete virtuale di Azure del cluster del servizio AzureKs. Sono disponibili diverse opzioni per stabilire la connettività di rete al cluster privato.

* Creare una rete virtuale nella stessa rete virtuale di Azure (VNet) del cluster AKS.
* Usare una macchina virtuale in una rete separata e configurare il [Peering di rete virtuale][virtual-network-peering].  Per altre informazioni su questa opzione, vedere la sezione riportata di seguito.
* Usare una connessione [ExpressRoute o VPN][express-route-or-VPN].
* Usare la [funzionalità del esegui comando del esegui comando AKS.](#aks-run-command-preview)

La creazione di una macchina virtuale nella stessa VNET del cluster AKS è l'opzione più semplice.  Express Route e VPN aggiungono costi e richiedono una maggiore complessità di rete.  Il peering di rete virtuale richiede la pianificazione degli intervalli CIDR della rete per assicurarsi che non siano presenti intervalli sovrapposti.

### <a name="aks-run-command-preview"></a>Servizio AKS esegui comando (anteprima)

Attualmente, quando è necessario accedere a un cluster privato, è necessario farlo all'interno della rete virtuale del cluster o di una rete o di un computer client con peering. Ciò richiede in genere che il computer sia connesso tramite VPN o Express Route alla rete virtuale del cluster o a un jumpbox da creare nella rete virtuale del cluster. Il comando AKS run consente di richiamare in remoto i comandi in un cluster del servizio AppKs tramite l'API del servizio AppKs. Questa funzionalità offre un'API che consente, ad esempio, di eseguire comandi just-in-time da un computer portatile remoto per un cluster privato. Ciò può essere molto utile per l'accesso JUST-In-Time rapido a un cluster privato quando il computer client non si trova nella rete privata del cluster mantenendo e forzando gli stessi controlli controllo degli accessi in base al ruolo e il server API privato.

### <a name="register-the-runcommandpreview-preview-feature"></a>Registrare la `RunCommandPreview` funzionalità di anteprima

Per usare la nuova API esegui comando, è necessario abilitare il `RunCommandPreview` flag di funzionalità nella sottoscrizione.

Registrare `RunCommandPreview` il flag di funzionalità usando il comando [az feature register][az-feature-register], come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il [comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

Al termine, aggiornare la registrazione del provider *di risorse Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>Usare il esegui comando del esegui comando

Comando semplice

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Distribuire un manifesto allegando il file specifico

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Distribuire un manifesto allegando un'intera cartella

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Eseguire un'installazione helm e passare il manifesto dei valori specifici

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Peering di rete virtuale

Come accennato, il peering di rete virtuale è un modo per accedere al cluster privato. Per usare il peering di rete virtuale, è necessario configurare un collegamento tra la rete virtuale e la zona DNS privata.
    
1. Passare al gruppo di risorse del nodo nella portale di Azure.  
2. Selezionare la zona DNS privato.   
3. Nel riquadro di sinistra selezionare il collegamento **Rete virtuale**.  
4. Creare un nuovo collegamento per aggiungere la rete virtuale della macchina virtuale alla zona DNS privata. Sono necessari alcuni minuti per rendere disponibile il collegamento per la zona DNS.  
5. Nel portale di Azure passare al gruppo di risorse che contiene la rete virtuale del cluster.  
6. Nel riquadro a destra, selezionare la rete virtuale. Il nome della rete virtuale è nel modulo *aks-vnet-\** .  
7. Nel riquadro a sinistra selezionare **Peering**.  
8. Selezionare **Aggiungi**, aggiungere la rete virtuale della macchina virtuale, quindi creare il peering.  
9. Passare alla rete virtuale in cui è presente la macchina virtuale, selezionare **Peering**, selezionare la rete virtuale AKS, quindi creare il peering. Se gli intervalli di indirizzi nella rete virtuale AKS e nella rete virtuale della macchina virtuale sono in conflitto, il peering ha esito negativo. Per altre informazioni, vedere [Peering di rete virtuale][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e spoke con DNS personalizzato

[Le architetture hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) vengono comunemente usate per distribuire le reti in Azure. In molte di queste distribuzioni, le impostazioni DNS nelle reti virtuali spoke sono configurate in modo da fare riferimento a un server d'inoltro DNS centrale per consentire la risoluzione DNS locale e basata su Azure. Quando si distribuisce un cluster AKS in un ambiente di rete di questo tipo è necessario tenere conto di alcune considerazioni speciali.

![Hub e spoke del cluster privato](media/private-clusters/aks-private-hub-spoke.png)

1. Per impostazione predefinita, quando viene effettuato il provisioning di un cluster privato, nel gruppo di risorse gestito dal cluster vengono creati un endpoint privato (1) e una zona DNS privata (2). Il cluster usa un record A nella zona privata per risolvere l'indirizzo IP dell'endpoint privato per la comunicazione con il server API.

2. La zona DNS privata è collegata solo alla VNet a cui sono collegati i nodi del cluster (3). Ciò significa che l'endpoint privato può essere risolto solo dagli host in tale VNet collegata. Negli scenari in cui non è configurato alcun DNS personalizzato nella rete virtuale (impostazione predefinita), questa operazione funziona senza problemi perché gli host puntano a 168.63.129.16 per DNS in grado di risolvere i record nella zona DNS privata a causa del collegamento.

3. Negli scenari in cui la VNet che contiene il cluster dispone di impostazioni DNS personalizzate (4), la distribuzione del cluster ha esito negativo a meno che la zona DNS privata non sia collegata alla VNet che contiene i resolver DNS personalizzati (5). Questo collegamento può essere creato manualmente dopo la creazione della zona privata durante il provisioning del cluster o tramite automazione al rilevamento della creazione della zona tramite meccanismi di distribuzione basati su eventi, ad esempio Griglia di eventi di Azure e Funzioni di Azure.

> [!NOTE]
> Se si usa [Bring Your Own Route Table con kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) e Bring Your Own DNS con cluster privato, la creazione del cluster avrà esito negativo. Sarà necessario associare [routeTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) nel gruppo di risorse del nodo alla subnet dopo che la creazione del cluster non è riuscita, per completare la creazione.

## <a name="limitations"></a>Limitazioni 
* Gli intervalli ip autorizzati non possono essere applicati all'endpoint del server API privato, ma solo al server API pubblico
* [Le limitazioni del servizio Collegamento privato di Azure][private-link-service] si applicano ai cluster privati.
* Nessun supporto per gli agenti ospitati da Microsoft di Azure DevOps con cluster privati. Si consiglia di usare gli [agenti self-hosted](/azure/devops/pipelines/agents/agents?tabs=browser). 
* Per i clienti che devono consentire al Registro contenitori di Azure di lavorare con il servizio Azure Kubernetes, è necessario eseguire il peering della rete virtuale Registro contenitori con la rete virtuale del cluster di agenti.
* Nessun supporto per la conversione di cluster AKS esistenti in cluster privati
* Se si elimina o si modifica l'endpoint privato nella subnet del cliente, il cluster smette di funzionare. 
* Dopo che i clienti hanno aggiornato il record A nei propri server DNS, tali pod risolvono comunque l'FQDN del server API nell'indirizzo IP precedente dopo la migrazione fino al riavvio. I clienti devono riavviare hostNetwork Pods e default-DNSPolicy Pods dopo la migrazione del piano di controllo.
* In caso di manutenzione sul piano di controllo, l'indirizzo IP del servizio [Web Diaks](./limit-egress-traffic.md) potrebbe cambiare. In questo caso è necessario aggiornare il record A che punta all'INDIRIZZO IP privato del server API nel server DNS personalizzato e riavviare eventuali pod o distribuzioni personalizzati usando hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
