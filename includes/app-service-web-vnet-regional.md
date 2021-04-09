---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609513"
---
L'uso dell'integrazione VNet a livello di area consente all'app di accedere a:

* Risorse in un VNet nella stessa area dell'app.
* Risorse in reti virtuali con peering al VNet con cui è integrata l'app.
* Servizi protetti dell'endpoint di servizio.
* Risorse tra le connessioni Azure ExpressRoute.
* Risorse nella VNet con cui si è integrato.
* Risorse tra connessioni con peering, incluse le connessioni di Azure ExpressRoute.
* Endpoint privati 

Quando si usa l'integrazione di VNet con reti virtuali nella stessa area, è possibile usare le funzionalità di rete di Azure seguenti:

* **Gruppi di sicurezza di rete (gruppi)**: è possibile bloccare il traffico in uscita con una NSG posizionata nella subnet di integrazione. Le regole in ingresso non si applicano perché non è possibile usare l'integrazione VNet per fornire l'accesso in ingresso all'app.
* **Tabelle di route (UDR)**: è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita laddove si vuole.

Per impostazione predefinita, l'app instrada solo il traffico RFC1918 in VNet. Se si vuole instradare tutto il traffico in uscita nella VNet, seguire questa procedura per aggiungere l' `WEBSITE_VNET_ROUTE_ALL` impostazione nell'app: 

1. Passare all'interfaccia utente di **configurazione** nel portale dell'app. Selezionare **Nuova impostazione applicazione**.
1. Immettere `WEBSITE_VNET_ROUTE_ALL` nella casella **nome** e immettere `1` nella casella **valore** .

   ![Specificare l'impostazione dell'applicazione][4]

1. Selezionare **OK**.
1. Selezionare **Salva**.

> [!NOTE]
> Quando si esegue il routing di tutto il traffico in uscita nel VNet, è soggetto a gruppi e UdR applicati alla subnet di integrazione. Quando `WEBSITE_VNET_ROUTE_ALL` è impostato su `1` , il traffico in uscita viene ancora inviato dagli indirizzi elencati nelle proprietà dell'app, a meno che non si forniscano route che indirizzano il traffico altrove.
> 
> L'integrazione VNet a livello di area non è in grado di utilizzare la porta 25.

Esistono alcune limitazioni all'uso dell'integrazione di VNet con reti virtuali nella stessa area:

* Non è possibile raggiungere risorse tra connessioni di peering globali.
* La funzionalità è disponibile in tutte le unità di scala del servizio app in Premium v2 e Premium V3. È anche disponibile in standard ma solo da unità di scala del servizio app più recenti. Se si usa un'unità di scala precedente, è possibile usare la funzionalità solo da un piano di servizio App Premium v2. Per assicurarsi che sia possibile usare la funzionalità in un piano di servizio app standard, creare l'app in un piano di servizio App Premium V3. Tali piani sono supportati solo nelle unità di scala più recenti. Se lo si desidera, è possibile ridurre le prestazioni.  
* La subnet di integrazione può essere usata da un solo piano di servizio app.
* La funzionalità non può essere usata da app del piano isolato che si trovano in un ambiente del servizio app.
* Per la funzionalità è necessaria una subnet inutilizzata/28 o superiore in un Azure Resource Manager VNet.
* L'app e il VNet devono trovarsi nella stessa area.
* Non è possibile eliminare un VNet con un'app integrata. Rimuovere l'integrazione prima di eliminare il VNet.
* È possibile avere una sola integrazione VNet a livello di area per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare lo stesso VNet.
* Non è possibile modificare la sottoscrizione di un'app o di un piano mentre è presente un'app che usa l'integrazione VNet a livello di area.
* L'app non può risolvere gli indirizzi in Zone private di DNS di Azure senza modifiche di configurazione.

L'integrazione di VNet dipende da una subnet dedicata. Quando si esegue il provisioning di una subnet, la subnet di Azure perde cinque indirizzi IP dall'inizio. Un indirizzo viene usato dalla subnet di integrazione per ogni istanza del piano. Quando si ridimensiona l'app a quattro istanze, vengono usati quattro indirizzi. 

Quando si aumentano o diminuiscono le dimensioni, lo spazio degli indirizzi richiesto viene raddoppiato per un breve periodo di tempo. Ciò influiscono sulle istanze effettive supportate disponibili per una determinata dimensione della subnet. La tabella seguente mostra sia il numero massimo di indirizzi disponibili per ogni blocco CIDR che l'effetto sulla scalabilità orizzontale:

| Dimensioni blocco CIDR | Numero massimo di indirizzi disponibili | Scalabilità orizzontale massima (istanze)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Si presuppone che sia necessario aumentare o ridurre le dimensioni in un determinato punto. 

Poiché le dimensioni della subnet non possono essere modificate dopo l'assegnazione, usare una subnet sufficientemente grande da contenere la scala che l'app può raggiungere. Per evitare problemi con la capacità della subnet, è necessario usare un/26 con indirizzi 64.  

Quando si vuole che le app in un altro piano raggiungano un VNet già connesso da app in un altro piano, selezionare una subnet diversa da quella usata dall'integrazione VNet preesistente.

La funzionalità è completamente supportata per le app Windows e Linux, inclusi i [contenitori personalizzati](../articles/app-service/quickstart-custom-container.md). Tutti i comportamenti funzionano allo stesso modo tra app di Windows e app Linux.

### <a name="service-endpoints"></a>Endpoint di servizio

L'integrazione VNet a livello di area consente di raggiungere i servizi di Azure protetti con gli endpoint di servizio. Per accedere a un servizio protetto con endpoint di servizio, è necessario eseguire le operazioni seguenti:

1. Configurare l'integrazione di VNet a livello di area con l'app Web per connettersi a una subnet specifica per l'integrazione.
1. Passare al servizio di destinazione e configurare gli endpoint di servizio nella subnet di integrazione.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

È possibile usare i gruppi di sicurezza di rete per bloccare il traffico in ingresso e in uscita verso le risorse in una VNet. Un'app che usa l'integrazione VNet a livello di area può usare un [gruppo di sicurezza di rete][VNETnsg] per bloccare il traffico in uscita verso le risorse in VNet o Internet. Per bloccare il traffico verso gli indirizzi pubblici, è necessario che l'impostazione dell'applicazione sia `WEBSITE_VNET_ROUTE_ALL` impostata su `1` . Le regole in ingresso in un NSG non si applicano all'app perché l'integrazione con VNet influiscono solo sul traffico in uscita dall'app.

Per controllare il traffico in ingresso verso l'app, usare la funzionalità restrizioni di accesso. Un NSG applicato alla subnet di integrazione è attivo indipendentemente dalle route applicate alla subnet di integrazione. Se `WEBSITE_VNET_ROUTE_ALL` è impostato su `1` e non sono presenti route che influiscono sul traffico degli indirizzi pubblici sulla subnet di integrazione, tutto il traffico in uscita è ancora soggetto a gruppi assegnati alla subnet di integrazione. Quando `WEBSITE_VNET_ROUTE_ALL` non è impostato, gruppi viene applicato solo al traffico RFC1918.

### <a name="routes"></a>Route

È possibile usare le tabelle di route per instradare il traffico in uscita dall'app a qualsiasi posizione. Per impostazione predefinita, le tabelle di route influiscono solo sul traffico di destinazione RFC1918. Quando si imposta `WEBSITE_VNET_ROUTE_ALL` su `1` , tutte le chiamate in uscita sono interessate. Le route impostate nella subnet di integrazione non influiranno sulle risposte alle richieste di app in ingresso. Le destinazioni comuni possono includere dispositivi firewall o gateway.

Se si vuole instradare tutto il traffico in uscita in locale, è possibile usare una tabella di route per inviare tutto il traffico in uscita al gateway ExpressRoute. Se si esegue il routing del traffico a un gateway, assicurarsi di impostare le route nella rete esterna per restituire le risposte.

Le route Border Gateway Protocol (BGP) influiscono anche sul traffico dell'app. Se si hanno Route BGP da qualcosa come un gateway ExpressRoute, il traffico in uscita dell'app è interessato. Per impostazione predefinita, le route BGP hanno effetto solo sul traffico di destinazione RFC1918. Quando `WEBSITE_VNET_ROUTE_ALL` è impostato su `1` , tutto il traffico in uscita può essere influenzato dalle route BGP.

### <a name="azure-dns-private-zones"></a>Zone private di DNS di Azure 

Quando l'app si integra con la VNet, USA lo stesso server DNS con cui è configurata la VNet. Per impostazione predefinita, l'app non funziona con le zone private di DNS di Azure. Per lavorare con le zone private di DNS di Azure, è necessario aggiungere le impostazioni dell'app seguenti:

1. `WEBSITE_DNS_SERVER` con valore `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` con valore `1`

Queste impostazioni inviano tutte le chiamate in uscita dall'app in VNet e consentono all'app di accedere a una zona privata di DNS di Azure. Con queste impostazioni, l'app può usare il servizio DNS di Azure eseguendo una query sulla zona privata DNS a livello di ruolo di lavoro.  

### <a name="private-endpoints"></a>Endpoint privati

Se si desidera effettuare chiamate a [endpoint privati][privateendpoints], è necessario assicurarsi che le ricerche DNS vengano risolte nell'endpoint privato. È possibile applicare questo comportamento in uno dei modi seguenti: 

* Eseguire l'integrazione con le zone private di DNS di Azure. Quando il VNet non dispone di un server DNS personalizzato, questa operazione viene eseguita automaticamente.
* Gestire l'endpoint privato nel server DNS usato dall'app. A tale scopo, è necessario conoscerlo, quindi puntare l'endpoint che si sta provando a raggiungere l'indirizzo usando un record A.
* Configurare il server DNS in modo che si inoltri alle zone private di DNS di Azure.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
