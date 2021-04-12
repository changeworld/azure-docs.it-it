---
title: Procedure consigliate per le risorse di rete
titleSuffix: Azure Kubernetes Service
description: Informazioni sulle procedure consigliate per l'operatore del cluster per la connettività e le risorse di rete virtuale nel servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104950"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la sicurezza e la connettività di rete nel servizio Azure Kubernetes

Quando si creano e si gestiscono cluster nel servizio Azure Kubernetes, viene fornita la connettività di rete per nodi e applicazioni. Queste risorse di rete includono gli intervalli di indirizzi IP, i servizi di bilanciamento del carico e i controller in ingresso. Per mantenere un'elevata qualità del servizio per le applicazioni, è necessario elaborare strategie e configurare queste risorse.

Questo articolo sulle procedure consigliate è incentrato sulla sicurezza e la connettività di rete per gli operatori del cluster. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Confrontare le modalità di rete kubenet e Azure container Network Interface (CNI) in AKS.
> * Pianificare la connettività e gli indirizzi IP necessari.
> * Distribuire il traffico usando i bilanciamenti del carico, i controller in ingresso o un web application firewall (WAF).
> * Connettersi in modo sicuro ai nodi del cluster.

## <a name="choose-the-appropriate-network-model"></a>Scegliere il modello di rete appropriato

> **Indicazioni sulle procedure consigliate** 
> 
> Usare la rete CNI di Azure in AKS per l'integrazione con reti virtuali esistenti o reti locali. Questo modello di rete consente una maggiore separazione delle risorse e dei controlli in un ambiente aziendale.

Le reti virtuali forniscono la connettività di base per consentire ai clienti e ai nodi del servizio Azure Kubernetes di accedere alle applicazioni. Esistono due diversi modi per distribuire i cluster del servizio Azure Kubernetes nelle reti virtuali:

* **Rete di Azure CNI**

    Viene distribuito in una rete virtuale e usa il plug-in [Azure CNI][cni-networking] Kubernetes. Ai pod vengono assegnati IP singoli che possono essere instradati ad altri servizi di rete o a risorse locali.
* **Rete Kubenet**

    Azure gestisce le risorse della rete virtuale durante la distribuzione del cluster e usa il plug-in [kubenet][kubenet] Kubernetes.


Per le distribuzioni di produzione, kubenet e Azure CNI sono opzioni valide.

### <a name="cni-networking"></a>Rete CNI

Azure CNI è un protocollo indipendente dal fornitore che consente al runtime del contenitore di effettuare richieste a un provider di rete. Assegna gli indirizzi IP ai pod e ai nodi e fornisce le funzionalità di gestione indirizzi IP quando ci si connette alle reti virtuali di Azure esistenti. Ogni nodo e risorsa Pod riceve un indirizzo IP nella rete virtuale di Azure. non è necessario il routing aggiuntivo per comunicare con altre risorse o servizi.

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

In particolare, la rete di Azure CNI per la produzione consente la separazione del controllo e della gestione delle risorse. Dal punto di vista della sicurezza, è spesso preferibile che siano team diversi a gestire e proteggere tali risorse. Con la rete CNI di Azure è possibile connettersi a risorse di Azure esistenti, a risorse locali o ad altri servizi direttamente tramite indirizzi IP assegnati a ogni pod.

Quando si usano le funzionalità di rete Azure CNI, la risorsa di rete virtuale si trova in un gruppo di risorse separato rispetto al cluster del servizio Azure Kubernetes. Delegare le autorizzazioni per l'identità del cluster AKS per accedere e gestire queste risorse. L'identità del cluster usata dal cluster AKS deve avere almeno le autorizzazioni di [collaboratore rete](../role-based-access-control/built-in-roles.md#network-contributor) nella subnet nella rete virtuale. 

Se si vuole definire un [ruolo personalizzato](../role-based-access-control/custom-roles.md) invece di usare il ruolo predefinito Collaboratore di rete, sono necessarie le autorizzazioni seguenti:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Per impostazione predefinita, AKS usa un'identità gestita per l'identità del cluster. Tuttavia, è possibile usare un'entità servizio. Per altre informazioni:
* Delega dell'entità servizio AKS, vedere delegare [l'accesso ad altre risorse di Azure][sp-delegation]. 
* Identità gestite, vedere [usare identità gestite](use-managed-identity.md).

Poiché ogni nodo e Pod riceve il proprio indirizzo IP, pianificare gli intervalli di indirizzi per le subnet AKS. Tenere presente quanto segue:
* La subnet deve essere sufficientemente grande da fornire gli indirizzi IP per ogni nodo, Pod e risorsa di rete da distribuire. 
    * Con la rete kubenet e Azure CNI, ogni nodo che esegue ha limiti predefiniti al numero di Pod.
* Ogni cluster del servizio Azure Kubernetes deve essere inserito nella relativa subnet. 
* Evitare di usare intervalli di indirizzi IP che si sovrappongono a risorse di rete esistenti. 
    * Necessario per consentire la connettività a reti locali o con peering in Azure.
* Per gestire gli eventi di scalabilità orizzontale o gli aggiornamenti del cluster, sono necessari indirizzi IP aggiuntivi disponibili nella subnet assegnata. 
    * Questo spazio degli indirizzi aggiuntivo è particolarmente importante se si usano i contenitori di Windows Server, perché i pool di nodi richiedono un aggiornamento per applicare le patch di sicurezza più recenti. Per altre informazioni sui nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

Per calcolare l'indirizzo IP richiesto, vedere [Configure Azure CNI networking in Azure Kubernetes Service (AKS)][advanced-networking] (Configurare le funzionalità di rete Azure CNI nel servizio Azure Kubernetes).

Quando si crea un cluster con Azure CNI networking, si specificano altri intervalli di indirizzi per il cluster, ad esempio l'indirizzo del Bridge Docker, l'indirizzo IP del servizio DNS e l'intervallo di indirizzi del servizio. In generale, verificare che gli intervalli di indirizzi seguenti:
* Non sovrapporsi.
* Non sovrapporsi ad alcuna rete associata al cluster, incluse le reti virtuali, le subnet, le reti locali e con peering. 

Per informazioni dettagliate sui limiti e sul dimensionamento per questi intervalli di indirizzi, vedere [configurare la rete CNI di Azure in AKS][advanced-networking].

### <a name="kubenet-networking"></a>Funzionalità di rete kubenet

Sebbene kubenet non richieda di configurare le reti virtuali prima che il cluster venga distribuito, sono disponibili svantaggi nell'attesa:

* Poiché i nodi e i Pod si trovano in subnet IP diverse, il routing definito dall'utente (UDR) e l'inoltro IP instrada il traffico tra i pod e i nodi. Questo routing aggiuntivo può ridurre le prestazioni della rete.
* Le connessioni alle reti locali esistenti o le operazioni di peering con altre reti virtuali di Azure possono risultare complesse.

Poiché non si creano la rete virtuale e le subnet separatamente dal cluster AKS, Kubenet è ideale per:
* Carichi di lavoro di sviluppo o test di piccole dimensioni. 
* Siti Web semplici con traffico ridotto.
* Sollevamento e spostamento dei carichi di lavoro in contenitori.

Per la maggior parte delle distribuzioni di produzione, è consigliabile effettuare la pianificazione e usare le funzionalità di rete Azure CNI.

È anche possibile [configurare gli intervalli di indirizzi IP e le reti virtuali usando kubenet][aks-configure-kubenet-networking]. Analogamente alla rete di Azure CNI, questi intervalli di indirizzi non devono sovrapporsi e non devono sovrapporsi ad alcuna rete associata al cluster (reti virtuali, subnet, locali e reti con peering). 

Per informazioni dettagliate sui limiti e sul dimensionamento per questi intervalli di indirizzi, vedere [usare la rete kubenet con gli intervalli di indirizzi IP in AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuire il traffico in ingresso

> **Indicazioni sulle procedure consigliate** 
> 
> Per distribuire il traffico HTTP o HTTPS alle applicazioni, usare le risorse e i controller in ingresso. Rispetto a un servizio di bilanciamento del carico di Azure, i controller di ingresso offrono funzionalità aggiuntive e possono essere gestiti come risorse Kubernetes native.

Mentre un servizio di bilanciamento del carico di Azure può distribuire il traffico dei clienti alle applicazioni nel cluster AKS, è limitato a comprendere il traffico. Una risorsa di bilanciamento del carico funziona al livello 4 e distribuisce il traffico in base al protocollo o alle porte. 

La maggior parte delle applicazioni Web che usano HTTP o HTTPS deve usare le risorse e i controller Kubernetes in ingresso, che funzionano al livello 7. I controller e le risorse in ingresso possono distribuire il traffico in base all'URL dell'applicazione e gestire la terminazione TLS/SSL. Il traffico in ingresso riduce anche il numero di indirizzi IP esposti e mappati. 

Con un servizio di bilanciamento del carico, ogni applicazione necessita in genere di un indirizzo IP pubblico che è stato assegnato e di cui viene eseguito il mapping al servizio nel cluster del servizio Azure Kubernetes. Con una risorsa in ingresso, un singolo indirizzo IP può distribuire il traffico a più applicazioni.

![Diagramma che mostra il flusso del traffico in ingresso in un cluster del servizio Azure Kubernetes](media/operator-best-practices-network/aks-ingress.png)

 Esistono due componenti per l'ingresso:

 * Una *risorsa* in ingresso
 * Un *controller* di ingresso

### <a name="ingress-resource"></a>Risorsa in ingresso

La *risorsa in ingresso* è un manifesto YAML di `kind: Ingress` . Definisce l'host, i certificati e le regole per instradare il traffico ai servizi in esecuzione nel cluster AKS. 

L'esempio di manifesto YAML seguente consente di distribuire il traffico per *myapp.com* a uno dei due servizi, ovvero *blog* o *storeservice*. Il cliente viene indirizzato all'uno o all'altro servizio in base all'URL a cui accede.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

### <a name="ingress-controller"></a>Controller di ingresso

Un *controller di ingresso* è un daemon eseguito su un nodo AKS che controlla le richieste in ingresso. Il traffico viene quindi distribuito in base alle regole definite nella risorsa in ingresso. Sebbene il controller di ingresso più comune sia basato su [nginx], AKS non limita l'utente a un controller specifico. È possibile usare [Contour][contour], [HAProxy][haproxy], [Traefik][traefik]e così via.

I controller di ingresso devono essere pianificati in un nodo Linux. Indicare che la risorsa deve essere eseguita in un nodo basato su Linux usando un selettore di nodo nel manifesto YAML o nella distribuzione del grafico Helm. Per altre informazioni, vedere [usare i selettori di nodo per controllare dove sono pianificati i pod in AKS][concepts-node-selectors].

> [!NOTE]
> I nodi di Windows Server non devono eseguire il controller di ingresso.

Esistono molti scenari per l'ingresso, tra cui le guide pratiche seguenti:

* [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
* [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
* [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
* Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Proteggere il traffico con un web application firewall (WAF)

> **Indicazioni sulle procedure consigliate**
> 
> Per analizzare il traffico in ingresso per potenziali attacchi, usare un web application firewall (WAF), ad esempio [Barracuda WAF per Azure][barracuda-waf] o applicazione Azure gateway. Queste risorse di rete più avanzate possono anche instradare il traffico oltre solo le connessioni HTTP e HTTPS o la terminazione TLS di base.

Un controller di ingresso è in genere una risorsa Kubernetes nel cluster AKS che distribuisce il traffico ai servizi e alle applicazioni. Il controller viene eseguito come daemon in un nodo AKS e usa alcune delle risorse del nodo, ad esempio CPU, memoria e larghezza di banda di rete. Negli ambienti più grandi, è necessario:
* Eseguire l'offload di parte del routing del traffico o della terminazione TLS in una risorsa di rete all'esterno del cluster AKS.
* Analizza il traffico in ingresso per individuare potenziali attacchi.

![Un web application firewall (WAF), come il gateway applicazione di Azure, può proteggere e distribuire il traffico per il cluster del servizio Azure Kubernetes.](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Per questo livello di sicurezza aggiuntivo, un web application firewall (WAF) filtra il traffico in ingresso. Con un set di regole, il progetto Open Web Application Security (OWASP) si verifica per gli attacchi, ad esempio gli script tra siti o l'avvelenamento dei cookie. [Applicazione Azure gateway][app-gateway] (attualmente disponibile in anteprima in AKS) è un WAF che si integra con i cluster AKS, bloccando queste funzionalità di sicurezza prima che il traffico raggiunga le applicazioni e il cluster AKS. 

Poiché anche altre soluzioni di terze parti eseguono queste funzioni, è possibile continuare a usare gli investimenti o le competenze esistenti nel prodotto preferito.

Il servizio di bilanciamento del carico o le risorse in ingresso vengono continuamente eseguite nel cluster AKS e perfezionano la distribuzione del traffico. Il gateway applicazione può essere gestito centralmente come controller in ingresso con una definizione delle risorse. Per iniziare, [creare un controller in ingresso del gateway applicazione][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Controllare il flusso del traffico con criteri di rete

> **Indicazioni sulle procedure consigliate** 
>
> Usare i criteri di rete per consentire o negare il traffico ai pod. Per impostazione predefinita, è consentito tutto il traffico tra i pod in un cluster. Per garantire maggiore sicurezza, definire regole che limitino la comunicazione dei pod.

Criteri di rete è una funzionalità di Kubernetes disponibile in AKS che consente di controllare il flusso del traffico tra i pod. Si consente o si nega il traffico al pod in base a impostazioni quali le etichette, lo spazio dei nomi o la porta del traffico assegnati. I criteri di rete sono un modo nativo del cloud per controllare il flusso di traffico per i pod. Poiché i pod vengono creati dinamicamente in un cluster AKS, i criteri di rete richiesti possono essere applicati automaticamente.

Per usare i criteri di rete, abilitare la funzionalità quando si crea un nuovo cluster AKS. Non è possibile abilitare criteri di rete in un cluster esistente del servizio Azure Kubernetes. Pianificare in anticipo l'abilitazione dei criteri di rete nei cluster necessari. 

>[!NOTE]
>I criteri di rete devono essere usati solo per pod e nodi basati su Linux nel servizio Azure Kubernetes.

Si crea un criterio di rete come risorsa Kubernetes usando un manifesto YAML. I criteri vengono applicati ai pod definiti, con regole in ingresso o in uscita che definiscono il flusso del traffico. 

L'esempio seguente applica i criteri di rete ai pod a cui è stata applicata l'etichetta *app: backend*. La regola di ingresso consente solo il traffico da Pod con l'etichetta *app: frontend* :

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Per iniziare a usare i criteri, vedere [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Connettersi in modo sicuro ai nodi tramite un bastion host

> **Indicazioni sulle procedure consigliate** 
>
> Non esporre la connettività remota ai nodi AKS. Creare un bastion host, o una jump box, in una rete virtuale di gestione. Usare il bastion host per instradare in modo sicuro il traffico nel cluster del servizio Azure Kubernetes alle attività di gestione remota.

È possibile completare la maggior parte delle operazioni in AKS usando gli strumenti di gestione di Azure o il server API Kubernetes. I nodi AKS sono disponibili solo in una rete privata e non sono connessi alla rete Internet pubblica. Per connettersi ai nodi e fornire manutenzione e supporto, instradare le connessioni tramite un host Bastion o Jump box. Verificare che l'host si trovi in una rete virtuale di gestione separata con peering sicuro nella rete virtuale del cluster AKS.

![Connettersi ai nodi del servizio Azure Kubernetes usando un bastion host o una jump box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Anche la rete di gestione per il bastion host dovrebbe essere protetta. Usare un servizio [Azure ExpressRoute][expressroute] o un [gateway VPN][vpn-gateway] per connettersi a una rete locale e controllare l'accesso con i gruppi di sicurezza di rete.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è stato incentrato sulla sicurezza e la connettività di rete. Per altre informazioni sulle nozioni di base della rete in Kubernetes, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool