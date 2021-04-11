---
title: Concetti - Funzionalità di rete nel servizio Azure Kubernetes
description: Informazioni sulle funzionalità di rete nel servizio Azure Kubernetes, tra cui funzionalità di rete kubenet e Azure CNI, controller di ingresso, bilanciamento del carico e indirizzi IP statici.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105341"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes

In un approccio basato su contenitori di microservizi per lo sviluppo di applicazioni, i componenti dell'applicazione interagiscono per elaborare le attività. Kubernetes fornisce varie risorse per l'abilitazione di questa collaborazione:
* È possibile connettersi alle applicazioni ed esporle internamente o esternamente. 
* È possibile compilare applicazioni a disponibilità elevata tramite il bilanciamento del carico delle applicazioni. 
* Per le applicazioni più complesse, è possibile configurare il traffico in ingresso per la terminazione SSL/TLS o il routing di più componenti. 
* Per motivi di sicurezza, è possibile limitare il flusso del traffico di rete tra i pod e i nodi.

Questo articolo introduce i concetti di base correlati alle funzionalità di rete per le applicazioni nel servizio Azure Kubernetes:

- [Services](#services)
- [Reti virtuali di Azure](#azure-virtual-networks)
- [Controller in ingresso](#ingress-controllers)
- [Criteri di rete](#network-policies)

## <a name="kubernetes-basics"></a>Nozioni di base di Kubernetes

Per consentire l'accesso alle applicazioni o tra i componenti dell'applicazione, Kubernetes fornisce un livello di astrazione alla rete virtuale. I nodi Kubernetes si connettono a una rete virtuale, fornendo connettività in ingresso e in uscita per i pod. Il componente *kube-proxy* viene eseguito in ogni nodo per fornire queste funzionalità di rete.

In Kubernetes:
* I *Servizi* raggruppano logicamente i pod per consentire l'accesso diretto su una porta specifica tramite un indirizzo IP o un nome DNS. 
* È possibile distribuire il traffico usando un servizio di *bilanciamento del carico*. 
* Si può anche ottenere un routing più complesso del traffico dell'applicazione con i *controller di ingresso*. 
* La sicurezza e il filtraggio del traffico di rete per i pod sono possibili con i *criteri di rete* di Kubernetes.

La piattaforma Azure semplifica inoltre la rete virtuale per i cluster AKS. Quando si crea un servizio di bilanciamento del carico Kubernetes, viene creata e configurata anche la risorsa di Azure Load Balancer sottostante. Quando si aprono le porte di rete per i pod, vengono configurate le regole del gruppo di sicurezza di rete di Azure corrispondente. Per il routing delle applicazioni HTTP, Azure può anche configurare il *DNS esterno* quando vengono configurate nuove route in ingresso.

## <a name="services"></a>Servizi

Per semplificare la configurazione di rete per i carichi di lavoro dell'applicazione, Kubernetes usa *servizi* per raggruppare un set di pod in modo logico e fornire la connettività di rete. Sono disponibili i tipi di servizio seguenti:

- **IP del cluster** 
  
  Consente di creare un indirizzo IP interno da usare all'interno del cluster AKS. Soluzione idonea per le applicazioni solo interne che supportano altri carichi di lavoro all'interno del cluster.

    ![Diagramma che mostra il flusso del traffico IP del cluster in un cluster servizio Azure Kubernetes][aks-clusterip]

- **NodePort** 

  Crea un mapping di porte sul nodo sottostante che consente l'accesso all'applicazione direttamente con l'indirizzo IP e la porta del nodo.

    ![Diagramma che mostra il flusso del traffico NodePort in un cluster servizio Azure Kubernetes][aks-nodeport]

- **LoadBalancer** 

  Crea una risorsa di bilanciamento del carico di Azure, configura un indirizzo IP esterno e connette i pod richiesti al pool back-end del servizio di bilanciamento del carico. Per consentire al traffico dei clienti di raggiungere l'applicazione, le regole di bilanciamento del carico vengono create sulle porte desiderate. 

    ![Diagramma che illustra il flusso del traffico di bilanciamento del carico in un cluster servizio Azure Kubernetes][aks-loadbalancer]

    Per il controllo e il routing del traffico in ingresso aggiuntivi, è possibile usare un [controller di ingresso](#ingress-controllers).

- **ExternalName** 

  Crea una voce DNS specifica per semplificare l'accesso alle applicazioni.

I servizi di bilanciamento del carico e l'indirizzo IP dei servizi possono essere assegnati dinamicamente oppure è possibile specificare un indirizzo IP statico esistente. È possibile assegnare indirizzi IP statici interni ed esterni. Gli indirizzi IP statici esistenti sono spesso associati a una voce DNS.

È possibile creare un servizio di bilanciamento del carico *interno* ed *esterno* . Al servizio di bilanciamento del carico interno viene assegnato solo un indirizzo IP privato, pertanto non è possibile accedervi da Internet.

## <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Nel servizio Azure Kubernetes è possibile distribuire un cluster che usa uno dei due modelli di rete seguenti:

- Rete *Kubenet*

  Le risorse di rete vengono in genere create e configurate durante la distribuzione del cluster AKS.

- Rete *CNI (container Network Interface) di Azure*
 
  il cluster del servizio Azure Kubernetes viene connesso alle risorse di rete virtuale e alle configurazioni esistenti.

### <a name="kubenet-basic-networking"></a>Funzionalità di rete kubenet (di base)

L'opzione per le funzionalità di rete *kubenet* è la configurazione predefinita per la creazione del cluster del servizio Azure Kubernetes. Con *kubenet*:
1. I nodi ricevono un indirizzo IP dalla subnet della rete virtuale di Azure. 
1. I pod ricevono un indirizzo IP da uno spazio di indirizzi logicamente diverso rispetto alla subnet della rete virtuale di Azure dei nodi. 
1. Viene poi configurato il protocollo NAT (Network Address Translation) in modo che i pod possano raggiungere le risorse nella rete virtuale di Azure. 
1. L'indirizzo IP di origine del traffico viene convertito nell'indirizzo IP primario del nodo.

I nodi usano il plug-in [kubenet][kubenet] di Kubernetes. È possibile:
* Consentire alla piattaforma Azure di creare e configurare le reti virtuali per l'utente oppure 
* Scegliere di distribuire il cluster AKS in una subnet di rete virtuale esistente. 

Tenere presente che solo i nodi ricevono un indirizzo IP instradabile. I pod usano NAT per comunicare con altre risorse all'esterno del cluster AKS. Questo approccio riduce il numero di indirizzi IP che è necessario riservare nello spazio di rete per i pod da usare.

Per altre informazioni, vedere [Configurare funzionalità di rete kubenet per un cluster del servizio Azure Kubernetes][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Funzionalità di rete Azure CNI (avanzata)

Con Azure CNI ogni pod ottiene un indirizzo IP dalla subnet in modo che vi si possa accedere direttamente. Questi indirizzi IP devono essere pianificati in anticipo e univoci in tutto lo spazio di rete. Ogni nodo dispone di un parametro di configurazione per il numero massimo di Pod supportati. Il numero equivalente di indirizzi IP per nodo viene quindi riservato in primo piano. Senza alcuna pianificazione, questo approccio può compromettere l'esaurimento degli indirizzi IP o la necessità di ricompilare i cluster in una subnet più ampia man mano che le esigenze dell'applicazione aumentano.

A differenza di kubenet, il traffico verso gli endpoint nella stessa rete virtuale non è NAT all'indirizzo IP primario del nodo. L'indirizzo di origine per il traffico all'interno della rete virtuale è l'IP pod. Il traffico esterno alla rete virtuale resta NAT nell'IP primario del nodo.

I nodi usano il plug-in Kubernetes di [Azure CNI][cni-networking] .

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure][advanced-networking-diagram]

Per altre informazioni, vedere [Configurare Azure CNI in un cluster del servizio Azure Kubernetes][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Confrontare i modelli di rete

Sia kubenet che Azure CNI forniscono la connettività di rete per i cluster AKS. Esistono tuttavia vantaggi e svantaggi. A un livello elevato, si applicano le considerazioni seguenti:

* **kubenet**
    * Conserva lo spazio degli indirizzi IP.
    * Usa il servizio di bilanciamento del carico interno o esterno di Kubernetes per raggiungere i pod dall'esterno del cluster.
    * È possibile gestire e gestire manualmente le route definite dall'utente (UDR).
    * Massimo 400 nodi per cluster.
* **Azure CNI**
    * I pod ottengono la connettività di rete virtuale completa e possono essere raggiunti direttamente tramite il proprio indirizzo IP privato dalle reti connesse.
    * Richiede più spazio di indirizzi IP.

Tra kubenet e Azure CNI sono presenti le differenze di comportamento seguenti:

| Funzionalità                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Distribuire il cluster in una rete virtuale nuova o esistente                                            | Supportato-UdR applicato manualmente | Supportato |
| Connettività Pod-Pod                                                                         | Supportato | Supportato |
| Connettività Pod-VM; VM nella stessa rete virtuale                                          | Funziona quando avviato da Pod | Funziona in entrambi i modi |
| Connettività Pod-VM; VM in una rete virtuale con peering                                            | Funziona quando avviato da Pod | Funziona in entrambi i modi |
| Accesso locale tramite VPN o Express Route                                                | Funziona quando avviato da Pod | Funziona in entrambi i modi |
| Accesso alle risorse protette dagli endpoint del servizio                                             | Supportato | Supportato |
| Esporre i servizi Kubernetes usando un servizio di bilanciamento del carico, un gateway app o un controller di ingresso | Supportato | Supportato |
| DNS di Azure e zone private predefinite                                                          | Supportato | Supportato |

Per quanto riguarda DNS, con i plug-in kubenet e Azure CNI plugins DNS sono offerti da CoreDNS, una distribuzione eseguita in AKS con il proprio ridimensionamento automatico. Per altre informazioni su CoreDNS in Kubernetes, vedere [personalizzazione del servizio DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). Per impostazione predefinita, CoreDNS è configurato per l'invio di domini sconosciuti alla funzionalità DNS della rete virtuale di Azure in cui è distribuito il cluster AKS. Di conseguenza, le zone DNS e private di Azure funzioneranno per i pod in esecuzione in AKS.

### <a name="support-scope-between-network-models"></a>Ambito di supporto tra i modelli di rete

Indipendentemente dal modello di rete usato, sia kubenet che Azure CNI possono essere distribuiti in uno dei modi seguenti:

* La piattaforma Azure può creare e configurare automaticamente le risorse della rete virtuale quando si crea un cluster AKS.
* Quando si crea il cluster AKS, è possibile creare e configurare manualmente le risorse della rete virtuale e connetterle a tali risorse.

Sebbene le funzionalità come gli endpoint di servizio o UdR siano supportate sia con kubenet che con Azure CNI, i [criteri di supporto per AKS][support-policies] definiscono le modifiche che è possibile apportare. Ad esempio:

* Se si creano manualmente le risorse della rete virtuale per un cluster AKS, quando si configurano gli endpoint di servizio o UdR personalizzati, si è supportati.
* Se la piattaforma Azure crea automaticamente le risorse di rete virtuale per il cluster del servizio contenitore di Azure, non è possibile modificare manualmente le risorse gestite da AKS per configurare UdR o endpoint di servizio personalizzati.

## <a name="ingress-controllers"></a>Controller in ingresso

Quando si crea un servizio di tipo LoadBalancer, viene creata anche una risorsa di bilanciamento del carico di Azure sottostante. Il bilanciamento del carico viene configurato per distribuire il traffico verso i pod nel servizio su una porta specifica. 

Il LoadBalancer funziona solo a livello 4. Al livello 4, il servizio non è a conoscenza delle applicazioni effettive e non può eseguire altre considerazioni sul routing.

I *controller di ingresso* operano sul livello 7 e possono usare regole più intelligenti per distribuire il traffico delle applicazioni. I controller di ingresso instradano in genere il traffico HTTP a diverse applicazioni in base all'URL in ingresso.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster del servizio Azure Kubernetes][aks-ingress]

### <a name="create-an-ingress-resource"></a>Creare una risorsa di ingresso
In AKS è possibile creare una risorsa di ingresso usando NGINX, uno strumento analogo o la funzionalità di routing dell'applicazione HTTP AKS. Quando si abilita il routing delle applicazioni HTTP per un cluster servizio Azure Kubernetes, la piattaforma Azure crea il controller di ingresso e un controller *DNS esterno*. Quando vengono create le risorse di ingresso in Kubernetes, i record A DNS necessari vengono creati in una zona DNS specifica del cluster. 

Per altre informazioni, vedere [deploy http Application routing][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Controller di ingresso del gateway applicazione (AGIC)

Con il componente aggiuntivo controller di ingresso del gateway applicazione (AGIC), i clienti del servizio contenitore di Azure usano il servizio di bilanciamento del carico del gateway applicazione nativo di Azure per esporre il software cloud a Internet. AGIC monitora il cluster Kubernetes host e aggiorna continuamente un gateway applicazione, esponendo i servizi selezionati a Internet. 

Per altre informazioni sul componente aggiuntivo AGIC per AKS, vedere [che cos'è il controller di ingresso del gateway applicazione?][agic-overview].

### <a name="ssltls-termination"></a>Terminazione SSL/TLS

La terminazione SSL/TLS è un'altra funzionalità comune di ingresso. Nelle applicazioni Web di grandi dimensioni a cui si accede tramite HTTPS, la risorsa di ingresso gestisce la terminazione TLS anziché all'interno dell'applicazione stessa. Per fornire la configurazione e la generazione di certificati TLS automatici, è possibile configurare la risorsa di ingresso in modo da usare i provider, ad esempio "Let ' s Encrypt". 

Per altre informazioni sulla configurazione di un controller di ingresso NGINX con Let's Encrypt, vedere [Traffico in ingresso e TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Conservazione IP origine client

Configurare il controller di ingresso per mantenere l'IP di origine del client nelle richieste ai contenitori nel cluster AKS. Quando il controller di ingresso instrada la richiesta di un client a un contenitore nel cluster AKS, l'indirizzo IP di origine della richiesta non è disponibile per il contenitore di destinazione. Quando si Abilita la *conservazione dell'indirizzo IP di origine client*, l'indirizzo IP di origine per il client è disponibile nell'intestazione della richiesta in *X-inoltred-for*. 

Se si usa la conservazione dell'indirizzo IP di origine client sul controller di ingresso, non è possibile usare il pass-through TLS. La conservazione IP di origine client e il pass-through TLS possono essere usati con altri servizi, ad esempio il tipo *LoadBalancer* .

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un gruppo di sicurezza di rete filtra il traffico per le macchine virtuali come i nodi AKS. Quando si creano servizi, ad esempio LoadBalancer, la piattaforma Azure configura automaticamente le regole del gruppo di sicurezza di rete necessarie. 

Non è necessario configurare manualmente le regole del gruppo di sicurezza di rete per filtrare il traffico per i pod in un cluster AKS. È sufficiente definire le porte necessarie e l'invio come parte dei manifesti del servizio Kubernetes. Consentire alla piattaforma Azure di creare o aggiornare le regole appropriate. 

È anche possibile usare i criteri di rete per applicare automaticamente le regole di filtro del traffico ai pod.

## <a name="network-policies"></a>Criteri di rete

Per impostazione predefinita, tutti i pod in un cluster del servizio Azure Kubernetes possono inviare e ricevere traffico senza limitazioni. Per una maggiore sicurezza, definire le regole che controllano il flusso di traffico, ad esempio:
* Le applicazioni back-end vengono esposte solo ai servizi front-end richiesti. 
* I componenti del database sono accessibili solo ai livelli applicazione a cui si connettono.

Criteri di rete è una funzionalità di Kubernetes disponibile in AKS che consente di controllare il flusso del traffico tra i pod. Si consente o si nega il traffico al pod in base a impostazioni quali le etichette, lo spazio dei nomi o la porta del traffico assegnati. Anche se i gruppi di sicurezza di rete sono migliori per i nodi AKS, i criteri di rete sono un modo più appropriato per controllare il flusso di traffico per i pod. Poiché i pod vengono creati dinamicamente in un cluster AKS, i criteri di rete richiesti possono essere applicati automaticamente.

Per altre informazioni, vedere [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes][use-network-policies].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare le funzionalità di rete del servizio Azure Kubernetes, creare e configurare un cluster del servizio Azure Kubernetes con gli intervalli di indirizzi IP esistenti usando [kubenet][aks-configure-kubenet-networking] o [Azure CNI][aks-configure-advanced-networking].

Per le procedure consigliate associate, vedere procedure consigliate [per la connettività di rete e la sicurezza in AKS][operator-best-practices-network].

Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes/Cluster e carichi di lavoro del servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Accesso e identità per Kubernetes/servizio Azure Kubernetes][aks-concepts-identity]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
