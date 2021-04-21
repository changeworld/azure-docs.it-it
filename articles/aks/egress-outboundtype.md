---
title: Personalizzare le route definite dall'utente nel servizio Azure Kubernetes
description: Informazioni su come definire una route in uscita personalizzata nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773046"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Personalizzare l'uscita del cluster con una User-Defined predefinita

È possibile personalizzare l'uscita da un cluster del servizio Azure Kubernetes per adattarla a scenari specifici. Per impostazione predefinita, il servizio AKS eseguirà il provisioning di uno SKU Standard Load Balancer da configurare e usare per l'uscita. La configurazione predefinita potrebbe, però, non soddisfare i requisiti di tutti gli scenari se gli indirizzi IP pubblici non sono consentiti o se per l'uscita sono necessari hop aggiuntivi.

Questo articolo illustra in dettaglio come personalizzare la route in uscita di un cluster in modo da supportare scenari di rete personalizzati, ad esempio quelli che non consentono indirizzi IP pubblici e in cui il cluster deve trovarsi dietro un'appliance virtuale di rete.

## <a name="prerequisites"></a>Prerequisiti
* Interfaccia della riga di comando di Azure 2.0.81 o versione successiva
* Versione API `2020-01-01` o successiva


## <a name="limitations"></a>Limitazioni
* OutboundType può essere definito solo in fase di creazione del cluster e non può essere aggiornato in un secondo momento.
* Per impostare `outboundType`, sono necessari cluster del servizio Azure Kubernetes con `vm-set-type` impostato su `VirtualMachineScaleSets` e `load-balancer-sku` impostato su `Standard`.
* Per impostare `outboundType` sul valore `UDR`, è necessaria una route definita dall'utente con connettività in uscita valida per il cluster.
* L'impostazione di `outboundType` sul valore `UDR` implica che l'indirizzo IP di origine in ingresso indirizzato al servizio di bilanciamento del carico potrebbe **non corrispondere** all'indirizzo di destinazione in uscita del cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Panoramica dei tipi in uscita nel servizio Azure Kubernetes

È possibile personalizzare un cluster del servizio AKS con un `outboundType` univoco di `loadBalancer` tipo o `userDefinedRouting` .

> [!IMPORTANT]
> Il tipo in uscita influisce solo sul traffico in uscita del cluster. Per altre informazioni, vedere [Configurazione dei controller in ingresso.](ingress-basic.md)

> [!NOTE]
> È possibile usare la propria tabella [di route con][byo-route-table] la rete UDR e kubenet. Assicurarsi che l'identità del cluster (entità servizio o identità gestita) abbia le autorizzazioni di collaboratore per la tabella di route personalizzata.

### <a name="outbound-type-of-loadbalancer"></a>Tipo in uscita loadBalancer

Se `loadBalancer` l'opzione è impostata, il servizio AKS completa automaticamente la configurazione seguente. Il di bilanciamento del carico viene usato per l'uscita tramite un indirizzo IP pubblico assegnato dal servizio Azure Kubernetes. Il tipo in uscita `loadBalancer` supporta i servizi Kubernetes di tipo `loadBalancer`, che prevedono l'uscita dal bilanciamento del carico creato dal provider di risorse del servizio Azure Kubernetes.

La configurazione seguente viene eseguita dal servizio Web Del servizio Web Disatteso.
   * Viene effettuato il provisioning di un indirizzo IP pubblico per l'uscita del cluster.
   * L'indirizzo IP pubblico viene assegnato alla risorsa del bilanciamento del carico.
   * I pool back-end per il servizio di bilanciamento del carico sono impostati per i nodi agente nel cluster.

Di seguito è illustrata una topologia di rete distribuita nei cluster del servizio Azure Kubernetes per impostazione predefinita, in cui `outboundType` è impostato su `loadBalancer`.

![Il diagramma mostra I P e I P in ingresso, in cui il P in ingresso indirizza il traffico a un servizio di bilanciamento del carico, che indirizza il traffico da e verso un cluster interno e altro traffico verso l'I P in uscita, che indirizza il traffico verso Internet, M C R, i servizi necessari di Azure e il piano di controllo A K S.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo in uscita userDefinedRouting

> [!NOTE]
> L'uso di un tipo in uscita rappresenta uno scenario di rete avanzato e richiede una configurazione di rete appropriata.

Se `userDefinedRouting` è impostata, il servizio Web Disack non configurerà automaticamente i percorsi di uscita. La configurazione in uscita deve essere eseguita dall'utente.

Il cluster del servizio AzureKs deve essere distribuito in una rete virtuale esistente con una subnet configurata in precedenza perché quando non si usa l'architettura SLB (Standard Load Balancer), è necessario stabilire un'uscita esplicita. Di conseguenza, questa architettura richiede l'invio esplicito del traffico in uscita a un'appliance come un firewall, un gateway, un proxy o per consentire l'esecuzione della conversione degli indirizzi di rete (NAT) da parte di un indirizzo IP pubblico assegnato al servizio di bilanciamento del carico standard o all'appliance.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Creazione del servizio di bilanciamento del carico con userDefinedRouting

I cluster del servizio Azure Kubernetes con un tipo di UDR in uscita ricevono un servizio di bilanciamento del carico standard solo quando viene distribuito il primo servizio Kubernetes di tipo 'loadBalancer'. Il servizio di bilanciamento del carico è configurato con un indirizzo IP pubblico per le *richieste in* ingresso e un pool back-end per le *richieste in* ingresso. Le regole in ingresso vengono configurate dal provider di servizi cloud di Azure, ma non vengono configurati indirizzi **IP** pubblici in uscita o regole in uscita in seguito a un tipo di routing definito dall'utente in uscita. La UDR sarà comunque l'unica origine per il traffico in uscita.

I servizi di bilanciamento del carico di Azure [non comportano addebiti fino a quando non viene inserita una regola](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuire un cluster con tipo di uscita con route definita dall'utente e Firewall di Azure

Per illustrare l'applicazione di un cluster con tipo in uscita usando una route definita dall'utente, è possibile configurare un cluster in una rete virtuale con un Firewall di Azure nella propria subnet. Vedere questo esempio sull'esempio [di limitazione del traffico in uscita con firewall di Azure.](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)

> [!IMPORTANT]
> Per il tipo di route definita dall'utente in uscita è necessaria una route per 0.0.0.0/0 e la destinazione hop successivo dell'appliance virtuale di rete nella tabella di route.
> La tabella di route ha già un valore predefinito 0.0.0.0/0 per Internet, senza un indirizzo IP pubblico per SNAT la semplice aggiunta di questa route non fornirà l'uscita. Il servizio AKS convaliderà che non si crea una route 0.0.0.0/0 che punta a Internet, ma all'NVA o al gateway e così via. Quando si usa un tipo di routing definito dall'utente in uscita, non viene creato un indirizzo IP pubblico del servizio di bilanciamento del carico per le richieste **in** ingresso, a meno che non sia configurato un servizio di tipo *loadbalancer.* Un indirizzo IP pubblico per le richieste **in uscita non** viene mai creato dal servizio AKS se è impostato un tipo in uscita di UDR.

## <a name="next-steps"></a>Passaggi successivi

Vedere la [panoramica sulle route definite dall'utente per le reti Azure](../virtual-network/virtual-networks-udr-overview.md).

Vedere le informazioni su [come creare, modificare o eliminare una tabella di route](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
