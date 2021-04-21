---
title: Intervalli IP autorizzati del server API in servizio Azure Kubernetes (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 8fca3fe61e26a031e6ea09692c9ba0781bfca21f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769644"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteggere l'accesso al server API usando intervalli di indirizzi IP autorizzati in servizio Azure Kubernetes (AKS)

In Kubernetes il server API riceve le richieste di eseguire azioni nel cluster, ad esempio per creare risorse o ridimensionare il numero di nodi. Il server API è il modo centrale per interagire con e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, il server API deve essere accessibile solo da un set limitato di intervalli di indirizzi IP.

Questo articolo illustra come usare gli intervalli di indirizzi IP autorizzati del server API per limitare gli indirizzi IP e i CIDR che possono accedere al piano di controllo.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo illustra come creare un cluster del servizio AzureKs usando l'interfaccia della riga di comando di Azure.

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.76 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="limitations"></a>Limitazioni

La funzionalità Intervalli IP autorizzati del server API presenta le limitazioni seguenti:
- Nei cluster creati dopo che gli intervalli di indirizzi IP autorizzati del server API sono stati spostati dall'anteprima nell'ottobre 2019, gli intervalli di indirizzi IP autorizzati del server API sono supportati solo nel servizio di bilanciamento del carico SKU *Standard.* I cluster esistenti con il servizio di bilanciamento del carico *SKU* Basic e gli intervalli di indirizzi IP autorizzati del server API configurati continueranno a funzionare così come sono, ma non è possibile eseguirne la migrazione a un servizio di bilanciamento del carico SKU *Standard.* Questi cluster esistenti continueranno a funzionare anche se la versione o il piano di controllo di Kubernetes vengono aggiornati. Gli intervalli di indirizzi IP autorizzati del server API non sono supportati per i cluster privati.
- Questa funzionalità non è compatibile con i cluster che usano [l'indirizzo IP pubblico per nodo](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica degli intervalli IP autorizzati del server API

Il server API Kubernetes è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. Il servizio AKS fornisce un piano di controllo cluster a tenant singolo, con un server API dedicato. Per impostazione predefinita, al server API viene assegnato un indirizzo IP pubblico ed è necessario controllare l'accesso usando il controllo degli accessi in base al ruolo kubernetes (controllo degli accessi in base al ruolo di Kubernetes) o il controllo degli accessi in base al ruolo di Azure.

Per proteggere l'accesso al piano di controllo del servizio Web o al server API altrimenti accessibile pubblicamente, è possibile abilitare e usare intervalli IP autorizzati. Questi intervalli IP autorizzati consentono solo agli intervalli di indirizzi IP definiti di comunicare con il server API. Una richiesta inviata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati viene bloccata. Continuare a usare il controllo degli accessi in base al ruolo di Kubernetes o il controllo degli accessi in base al ruolo di Azure per autorizzare gli utenti e le azioni richieste.

Per altre informazioni sul server API e altri componenti del cluster, vedere Concetti di base [di Kubernetes per il servizio Kubernetes.][concepts-clusters-workloads]

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Creare un cluster del servizio Web Diaks con gli intervalli IP autorizzati del server API abilitati

Creare un cluster usando [il comando az aks create e][az-aks-create] specificare il parametro per fornire un elenco di *`--api-server-authorized-ip-ranges`* intervalli di indirizzi IP autorizzati. Questi intervalli di indirizzi IP sono in genere intervalli di indirizzi usati dalle reti locali o dagli indirizzi IP pubblici. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP nell'intervallo, ad esempio *137.117.106.88/29*.

> [!IMPORTANT]
> Per impostazione predefinita, il cluster usa il servizio di bilanciamento del carico [con SKU Standard][standard-sku-lb] che è possibile usare per configurare il gateway in uscita. Quando si abilitano gli intervalli IP autorizzati del server API durante la creazione del cluster, anche l'indirizzo IP pubblico per il cluster è consentito per impostazione predefinita oltre agli intervalli specificati. Se si specifica *"" o* nessun valore per , gli intervalli IP autorizzati *`--api-server-authorized-ip-ranges`* del server API verranno disabilitati. Si noti che se si usa PowerShell, usare (con segno *`--api-server-authorized-ip-ranges=""`* di uguale) per evitare problemi di analisi.

L'esempio seguente crea un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse *denominato myResourceGroup* con gli intervalli IP autorizzati del server API abilitati. Gli intervalli di indirizzi IP consentiti *sono 73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> È consigliabile aggiungere questi intervalli a un elenco elementi consentiti:
> - Indirizzo IP pubblico del firewall
> - Qualsiasi intervallo che rappresenta le reti da cui verrà amministrato il cluster
> - Se si usa un Azure Dev Spaces nel cluster del servizio Web Diaks, è necessario consentire intervalli [aggiuntivi in base all'area][dev-spaces-ranges].
>
> Il limite massimo per il numero di intervalli IP che è possibile specificare è 200.
>
> La propagazione delle regole può richiedere fino a 2 minuti. Attendere fino a quel momento durante il test della connessione.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Specificare gli indirizzi IP in uscita per il servizio di bilanciamento del carico SKU Standard

Quando si crea un cluster del servizio Gateway Gateway, se si specificano gli indirizzi IP in uscita o i prefissi per il cluster, sono consentiti anche questi indirizzi o prefissi. Ad esempio:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Nell'esempio precedente, tutti gli IP specificati nel parametro sono consentiti *`--load-balancer-outbound-ip-prefixes`* insieme agli IP nel parametro *`--api-server-authorized-ip-ranges`* .

È invece possibile specificare il parametro per consentire i *`--load-balancer-outbound-ip-prefixes`* prefissi IP del servizio di bilanciamento del carico in uscita.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Consentire solo l'indirizzo IP pubblico in uscita del servizio di bilanciamento del carico SKU Standard

Quando si abilitano gli intervalli IP autorizzati del server API durante la creazione del cluster, l'indirizzo IP pubblico in uscita per il servizio di bilanciamento del carico SKU Standard per il cluster è consentito anche per impostazione predefinita, oltre agli intervalli specificati. Per consentire solo l'indirizzo IP pubblico in uscita del servizio di bilanciamento del carico SKU Standard, usare *0.0.0.0/32* quando si specifica il *`--api-server-authorized-ip-ranges`* parametro .

Nell'esempio seguente è consentito solo l'indirizzo IP pubblico in uscita del servizio di bilanciamento del carico SKU Standard ed è possibile accedere al server API solo dai nodi all'interno del cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aggiornare gli intervalli IP autorizzati del server API di un cluster

Per aggiornare gli intervalli IP autorizzati del server API in un cluster esistente, usare il comando [az aks update][az-aks-update] e usare i parametri *`--api-server-authorized-ip-ranges`* ,--load-balancer-outbound-ip-prefixes *, *`--load-balancer-outbound-ips`* o--load-balancer-outbound-ip-prefixes.*

L'esempio seguente aggiorna gli intervalli IP autorizzati del server API nel cluster *denominato myAKSCluster* nel gruppo di risorse *denominato myResourceGroup*. L'intervallo di indirizzi IP da autorizzare *è 73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

È anche possibile usare *0.0.0.0/32* quando si specifica il parametro per consentire solo l'indirizzo IP pubblico del servizio di bilanciamento del carico *`--api-server-authorized-ip-ranges`* SKU Standard.

## <a name="disable-authorized-ip-ranges"></a>Disabilitare gli intervalli IP autorizzati

Per disabilitare gli intervalli IP autorizzati, usare [il comando az aks update][az-aks-update] e specificare un intervallo vuoto per disabilitare gli intervalli IP autorizzati del server API. Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Trovare gli intervalli IP autorizzati esistenti

Per trovare gli intervalli IP che sono stati autorizzati, usare [il comando az servizio][az-aks-show] Web Sdk show e specificare il nome e il gruppo di risorse del cluster. Ad esempio:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Aggiornare, disabilitare e trovare intervalli IP autorizzati usando portale di Azure

Le operazioni precedenti di aggiunta, aggiornamento, ricerca e disabilitazione degli intervalli IP autorizzati possono essere eseguite anche nel portale di Azure. Per accedere, passare a **Rete** in **Impostazioni** nel pannello dei menu della risorsa cluster.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="In un browser vengono visualizzate le impostazioni di rete della risorsa cluster portale di Azure pagina. Le opzioni &quot;imposta intervallo IP specificato&quot; e &quot;Intervalli IP specificati&quot; sono evidenziate.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Come trovare l'indirizzo IP da includere in `--api-server-authorized-ip-ranges` ?

È necessario aggiungere i computer di sviluppo, gli strumenti o gli indirizzi IP di automazione all'elenco di intervalli IP approvati del cluster del servizio AzureKs per accedere al server API da questa pagina. 

Un'altra opzione consiste nel configurare un JumpBox con gli strumenti necessari all'interno di una subnet separata nella rete virtuale di Firewall. Si presuppone che l'ambiente abbia un firewall con la rispettiva rete e che gli indirizzi IP del firewall siano stati aggiunti agli intervalli autorizzati. Analogamente, se è stato forzato il tunneling dalla subnet del servizio Web Del servizio Web di Servizio Web alla subnet firewall, anche il jumpbox nella subnet del cluster è a posto.

Aggiungere un altro indirizzo IP agli intervalli approvati con il comando seguente.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> L'esempio precedente aggiunge gli intervalli IP autorizzati del server API nel cluster. Per disabilitare gli intervalli IP autorizzati, usare il comando az aks update e specificare un intervallo vuoto "". 

Un'altra opzione è usare il comando seguente nei sistemi Windows per ottenere l'indirizzo IPv4 pubblico oppure è possibile usare la procedura descritta in [Trovare l'indirizzo IP.](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

È anche possibile trovare questo indirizzo cercando "qual è l'indirizzo IP" in un browser Internet.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati abilitati gli intervalli IP autorizzati del server API. Questo approccio fa parte del modo in cui è possibile eseguire un cluster del servizio Web Disartik sicuro.

Per altre informazioni, vedere Concetti relativi alla sicurezza per applicazioni e cluster nel servizio [Web AKS][concepts-security] e Procedure consigliate per la sicurezza e [gli aggiornamenti del cluster nel servizio Web Disack.][operator-best-practices-cluster-security]

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
