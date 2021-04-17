---
title: Integrare Firewall di Azure con Azure Load Balancer Standard
description: È possibile integrare un firewall di Azure in una rete virtuale con un servizio di bilanciamento del carico standard di Azure (pubblico o interno).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505905"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrare Firewall di Azure con Azure Load Balancer Standard

È possibile integrare un firewall di Azure in una rete virtuale con un servizio di bilanciamento del carico standard di Azure (pubblico o interno). 

La progettazione preferita prevede l'integrazione di un servizio di bilanciamento del carico interno con Firewall di Azure, poiché si tratta di una soluzione molto più semplice. È possibile usare un servizio di bilanciamento del carico pubblico se ne è già stato distribuito uno e si desidera mantenerlo. È tuttavia necessario tenere presente un problema di routing asimmetrico che può interrompere la funzionalità con lo scenario di bilanciamento del carico pubblico.

Per altre informazioni su Azure Load Balancer, vedere [Informazioni su Azure Load Balancer](../load-balancer/load-balancer-overview.md).

## <a name="public-load-balancer"></a>Bilanciamento del carico pubblico

Un servizio di bilanciamento del carico pubblico viene distribuito con un indirizzo IP front-end pubblico.

### <a name="asymmetric-routing"></a>Routing asimmetrico

Nel routing asimmetrico un pacchetto segue un percorso fino alla destinazione e un altro per tornare all'origine. Questo problema si verifica quando una subnet ha una route predefinita all'indirizzo IP privato del firewall e si usa un servizio di bilanciamento del carico pubblico. In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, elimina il pacchetto restituito perché il firewall non è a conoscenza di una sessione stabilita di questo tipo.

### <a name="fix-the-routing-issue"></a>Risolvere il problema di routing

Quando si distribuisce un firewall di Azure in una subnet, un passaggio consiste nel creare una route predefinita per la subnet che indirizza i pacchetti tramite l'indirizzo IP privato del firewall che si trova in AzureFirewallSubnet. Per altre informazioni, vedere [Tutorial: Deploy and configure Firewall di Azure using the portale di Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando si introduce il firewall nello scenario di bilanciamento del carico, si vuole che il traffico Internet in ingresso passi attraverso l'indirizzo IP pubblico del firewall, da cui il firewall applica le proprie regole e invia tramite NAT i pacchetti all'indirizzo IP pubblico del servizio di bilanciamento del carico. L'origine del problema è questa. I pacchetti giungono all'indirizzo IP pubblico del firewall, ma tornano al firewall tramite l'indirizzo IP privato (usando la route predefinita).
Per evitare questo problema, creare una route host aggiuntiva per l'indirizzo IP pubblico del firewall. I pacchetti trasmessi all'indirizzo IP pubblico del firewall vengono instradati tramite Internet. Questo evita di usare la route predefinita per indirizzo IP privato del firewall.

![Routing asimmetrico](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Esempio di tabella di route

Ad esempio, le route seguenti sono per un firewall all'indirizzo IP pubblico 20.185.97.136 e all'indirizzo IP privato 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabella di route](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Esempio di regola NAT

Nell'esempio seguente una regola NAT converte il traffico RDP nel firewall alla versione 20.185.97.136 nel servizio di bilanciamento del carico alla versione 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Regola NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Probe di integrità

Tenere presente che è necessario disporre di un servizio Web in esecuzione negli host nel pool di bilanciamento del carico se si usano probe di integrità TCP per la porta 80 o probe HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Servizio di bilanciamento del carico interno

Un servizio di bilanciamento del carico interno viene distribuito con un indirizzo IP front-end privato.

Questo scenario non presenta problemi di routing asimmetrico. I pacchetti in ingresso arrivano all'indirizzo IP pubblico del firewall, vengono convertiti nell'indirizzo IP privato del servizio di bilanciamento del carico e quindi tornano all'indirizzo IP privato del firewall usando lo stesso percorso di ritorno.

È quindi possibile distribuire questo scenario in modo simile allo scenario di bilanciamento del carico pubblico, ma senza la necessità della route host per l'indirizzo IP pubblico del firewall.

Le macchine virtuali nel pool back-end possono avere connettività Internet in uscita tramite Firewall di Azure. Configurare una route definita dall'utente nella subnet della macchina virtuale con il firewall come hop successivo.


## <a name="additional-security"></a>Sicurezza aggiuntiva

Per migliorare ulteriormente la sicurezza dello scenario con bilanciamento del carico, è possibile usare i gruppi di sicurezza di rete (NSG).

È ad esempio possibile creare un gruppo di sicurezza di rete nella subnet back-end in cui si trovano le macchine virtuali con bilanciamento del carico. Consentire il traffico in ingresso proveniente dall'indirizzo IP o dalla porta del firewall.

![Gruppo di sicurezza di rete](media/integrate-lb/nsg-01.png)

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza](../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare un Firewall di Azure](tutorial-firewall-deploy-portal.md).