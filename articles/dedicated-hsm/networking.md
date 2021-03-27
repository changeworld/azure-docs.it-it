---
title: Considerazioni sulla rete - Modulo di protezione hardware dedicato di Azure | Microsoft Docs
description: Panoramica delle considerazioni sulla rete applicabili alle distribuzioni del modulo di protezione hardware dedicato di Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 3370389027805cfb5a68b5b0551d14dc31154804
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611838"
---
# <a name="azure-dedicated-hsm-networking"></a>Reti con HSM dedicato di Azure

HSM dedicato di Azure richiede un ambiente di rete altamente sicuro. Questo vale sia dal cloud di Azure all'ambiente IT del cliente (locale), che con l'uso delle applicazioni distribuite e per scenari a disponibilità elevata. Rete di Azure fornisce tutto questo e vi sono quattro aree distinte a cui prestare attenzione.

- Creazione di dispositivi HSM all'interno della rete virtuale in Azure
- Connessione locale a risorse basate su cloud per la configurazione e gestione di dispositivi HSM
- Creazione e connessione di reti virtuali per l'interconnessione di risorse applicative e dispositivi HSM
- Connessione di reti virtuali tra aree per l'intercomunicazione e per abilitare la disponibilità elevata

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rete virtuale per HSM dedicati

Gli HSM dedicati sono integrati in una rete virtuale e inseriti nella rete privata del cliente in Azure. Ciò consente l'accesso ai dispositivi da macchine virtuali o risorse di calcolo nella rete virtuale.  
Per altre informazioni sull'integrazione dei servizi di Azure nella rete virtuale e le funzionalità che ciò offre, vedere la documentazione [Rete virtuale per servizi di Azure](../virtual-network/virtual-network-for-azure-services.md).

### <a name="virtual-networks"></a>Reti virtuali

Prima di eseguire il provisioning di un dispositivo HSM dedicato i clienti devono creare una rete virtuale in Azure o devono usarne una esistente nella loro sottoscrizione. La rete virtuale definisce il perimetro di sicurezza per il dispositivo HSM dedicato. Per altre informazioni sulla creazione di reti virtuali, vedere la [documentazione sulle reti virtuali](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Subnet

Le subnet segmentano la rete virtuale in spazi di indirizzi separati usabili dalle risorse di Azure contenute. Gli HSM dedicati sono distribuiti in una subnet all'interno di una rete virtuale. Ogni dispositivo HSM dedicato che viene distribuito nella subnet del cliente riceverà un indirizzo IP privato dalla subnet. La subnet in cui il dispositivo HSM viene distribuito deve essere delegata in modo esplicito al servizio: Microsoft.HardwareSecurityModules/dedicatedHSMs. In questo modo vengono concesse determinate autorizzazioni al servizio HSM per la distribuzione nella subnet. La delega agli HSM dedicati impone alcune limitazioni per i criteri della subnet. Nelle subnet delegate non sono supportati i gruppi di sicurezza di rete e le route definite dall'utente. Di conseguenza, dopo che una subnet è stata delegata a HSM dedicati, può essere usata solo per distribuire risorse HSM. Non sarà possibile distribuire nessun'altra risorsa del cliente nella subnet.


### <a name="expressroute-gateway"></a>Gateway ExpressRoute

Un requisito dell'architettura corrente è la configurazione di un gateway ExpressRoute nella subnet dei clienti in cui deve essere inserito un dispositivo HSM per abilitare l'integrazione del dispositivo HSM in Azure. Questo gateway ExpressRoute non può essere usato per la connessione di percorsi locali ai dispositivi HSM dei clienti in Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Connessione dell'IT locale ad Azure

Quando si creano risorse basate su cloud, un'esigenza tipica è mantenere una connessione privata a risorse IT locali. Nel caso di HSM dedicato, ciò sarà necessario soprattutto per consentire al software client HSM di configurare i dispositivi HSM e anche per attività quali il backup e il recupero dei log dai moduli di protezione hardware a scopo di analisi. Una decisione fondamentale da prendere a questo proposito riguarda la natura della connessione, per la quale esistono varie opzioni.  L'opzione più flessibile è VPN da sito a sito dato che probabilmente ci saranno più risorse locali che richiedono la comunicazione sicura con le risorse, inclusi i moduli di protezione hardware, nel cloud di Azure. Questo richiederà che l'organizzazione del cliente abbia un dispositivo VPN per facilitare la connessione. È possibile usare una connessione VPN da punto a sito se è presente un solo endpoint locale, ad esempio una sola workstation amministrativa.
Per altre informazioni sulle opzioni di connettività, vedere le [opzioni di pianificazione di Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Al momento ExpressRoute non può essere usato per la connessione a risorse locali. Si noti anche che il gateway ExpressRoute non può essere usato come descritto in precedenza per le connessioni all'infrastruttura locale.

### <a name="point-to-site-vpn"></a>VPN da punto a sito

Una rete privata virtuale da punto a sito è la forma più semplice di connessione sicura a un singolo endpoint locale. Può risultare utile se si prevede di avere una sola workstation amministrativa per HSM dedicati basati su Azure.

### <a name="site-to-site-vpn"></a>VPN da sito a sito

Una rete privata virtuale da sito a sito consente la comunicazione sicura tra HSM dedicati basati su Azure e l'IT locale. Per eseguire questa operazione, è necessario disporre di una funzionalità di backup per il modulo di protezione hardware locale e che sia necessaria una connessione tra i due per l'esecuzione del backup.

## <a name="connecting-virtual-networks"></a>Connessione di reti virtuali

Un'architettura di distribuzione tipica per HSM dedicato inizia con una singola rete virtuale e la corrispondente subnet in cui vengono creati i dispositivi HSM e ne viene eseguito il provisioning. All'interno della stessa area possono essere presenti anche altre reti virtuali e subnet per i componenti dell'applicazione che usano l'HSM dedicato. Per abilitare la comunicazione tra queste reti si usa il peering di rete virtuale.

### <a name="virtual-network-peering"></a>Peering di rete virtuale

Quando in un'area sono presenti più reti virtuali che devono accedere l'una alle risorse delle altre, si può usare il peering di rete virtuale per creare canali di comunicazione sicura tra esse.  Il peering di rete virtuale non solo consente la comunicazione sicura ma garantisce anche una connessione a bassa latenza e larghezza di banda elevata tra le risorse in Azure.

![peering di rete](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Connessione tra aree di Azure

I dispositivi HSM hanno la capacità, tramite le librerie di software, di reindirizzare il traffico a un modulo di protezione hardware alternativo. Il reindirizzamento del traffico è utile in caso di errore dei dispositivi o di perdita dell'accesso a essi. È possibile ridurre gli scenari di errore a livello di area distribuendo moduli di protezione hardware in altre aree e abilitando la comunicazione tra reti virtuali in aree diverse.

### <a name="cross-region-ha-using-vpn-gateway"></a>Disponibilità elevata tra aree mediante il gateway VPN

Per le applicazioni distribuite a livello globale o per gli scenari di failover a livello di area con disponibilità elevata è necessario connettere reti virtuali di aree diverse. Con HSM dedicato di Azure è possibile ottenere la disponibilità elevata usando un Gateway VPN che fornisce un tunnel sicuro tra le due reti virtuali. Per altre informazioni sulle connessioni da rete virtuale a rete virtuale mediante un Gateway VPN, vedere l'articolo intitolato [Che cos'è un Gateway VPN?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Al momento il peering di rete virtuale globale in scenari di connettività fra aree non è disponibile con HSM dedicato e al suo posto si deve usare un gateway VPN. 

![Il diagramma mostra due aree connesse da due gateway V P N. Ogni area contiene reti virtuali con peering.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Restrizioni di rete
> [!NOTE]
> Un vincolo del servizio HSM dedicato che usa la delega della subnet è soggetto a restrizioni che devono essere prese in considerazione quando si progetta l'architettura di rete di destinazione per una distribuzione HSM. L'uso della delega della subnet significa che gruppi, UdR e il peering VNet globale non sono supportati per il modulo di protezione hardware dedicato. Le sezioni seguenti forniscono supporto per le tecniche alternative per ottenere lo stesso risultato o un risultato simile per queste funzionalità. 

La scheda di interfaccia di rete HSM che risiede nel modulo di protezione hardware dedicato VNet non può usare i gruppi di sicurezza di rete o le route definite dall'utente. Ciò significa che non è possibile impostare i criteri di negazione predefiniti dal punto di vista del modulo di protezione hardware dedicato VNet e che altri segmenti di rete devono essere allowlisted per accedere al servizio HSM dedicato. 

L'aggiunta della soluzione proxy appliance virtuale di rete (NVA) consente anche di posizionare logicamente un firewall dell'appliance virtuale di rete nell'hub transito/rete perimetrale davanti alla scheda di interfaccia di rete del modulo di protezione hardware, fornendo in tal modo l'alternativa necessaria a gruppi e UdR.

### <a name="solution-architecture"></a>Architettura della soluzione
Questa progettazione di rete richiede gli elementi seguenti:
1.  Un hub di transito o rete perimetrale VNet con un livello proxy di appliance virtuale di rete. Idealmente sono presenti due o più appliance virtuali. 
2.  Un circuito ExpressRoute con un peering privato abilitato e una connessione all'hub di transito VNet.
3.  Peering VNet tra l'hub di transito VNet e il modulo di protezione hardware dedicato VNet.
4.  È possibile distribuire un firewall dell'appliance virtuale di rete o un firewall di Azure per offrire servizi DMZ nell'hub come opzione. 
5.  È possibile eseguire il peering di reti virtuali di carico di lavoro aggiuntivo per l'hub VNet. Il client Gemalto può accedere al servizio HSM dedicato tramite l'hub VNet.

![Il diagramma mostra un hub DMZ VNet con un livello proxy di appliance virtuale di rete per NSG e UDR soluzione alternativa](media/networking/network-architecture.png)

Poiché l'aggiunta della soluzione proxy per l'appliance virtuale di rete consente anche un firewall dell'appliance virtuale di rete nell'hub transito/rete perimetrale da posizionare logicamente davanti alla scheda di interfaccia di rete HSM, fornendo i criteri di negazione predefiniti necessari. In questo esempio, verrà usato il firewall di Azure per questo scopo e sono necessari gli elementi seguenti:
1. Un firewall di Azure distribuito nella subnet "AzureFirewallSubnet" nell'hub DMZ VNet
2. Una tabella di routing con una UDR che indirizza il traffico verso l'endpoint privato di Azure ILB al firewall di Azure. Questa tabella di routing verrà applicata a GatewaySubnet in cui risiede il gateway virtuale ExpressRoute del cliente
3. Regole di sicurezza di rete all'interno di AzureFirewall per consentire l'invio tra un intervallo di origine attendibile e l'endpoint privato di Azure IBL in ascolto sulla porta TCP 1792. Questa logica di sicurezza aggiungerà il criterio "default deny" necessario per il servizio HSM dedicato. Ciò significa che nel servizio HSM dedicato saranno consentiti solo gli intervalli IP di origine attendibili. Tutti gli altri intervalli verranno eliminati.  
4. Una tabella di routing con una UDR che indirizza il traffico verso l'ambiente locale nel firewall di Azure. Questa tabella di routing verrà applicata alla subnet del proxy di appliance virtuale di dispositivo. 
5. Un NSG applicato alla subnet dell'appliance virtuale di rete del proxy per considerare attendibile solo l'intervallo di subnet del firewall di Azure come origine e consentire solo l'inoltro all'indirizzo IP della NIC del modulo di protezione hardware sulla porta TCP 1792. 

> [!NOTE]
> Poiché il livello proxy di appliance virtuale di rete SNAT l'indirizzo IP del client mentre viene trasmesso alla scheda di interfaccia di rete HSM, non sono richiesti UdR tra le VNet HSM e l'hub DMZ VNet.  

### <a name="alternative-to-udrs"></a>Alternativa a UdR
La soluzione di livello Appliance precedente indicata sopra funziona come alternativa a UdR. Ci sono alcuni aspetti importanti da tenere presenti.
1.  La conversione degli indirizzi di rete deve essere configurata in appliance virtuale di rete per consentire il routing del traffico restituito correttamente.
2. I clienti devono disabilitare la configurazione dell'indirizzo IP client-check in luna HSM per usare VNA per NAT. I comandi seguenti servce come esempio.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  Distribuire UdR per il traffico in ingresso nel livello NVA. 
4. Per quanto riguarda la progettazione, le subnet HSM non avvierà una richiesta di connessione in uscita al livello piattaforma.

### <a name="alternative-to-using-global-vnet-peering"></a>Alternativa all'uso di Peering reti virtuali globali
Esistono due architetture che è possibile usare come alternativa al peering VNet globale.
1.  Usare la [connessione gateway VPN da VNET a VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 
2.  Connettere HSM VNET a un altro VNET con un circuito ER. Questa operazione è ottimale quando è necessario un percorso locale diretto o VNET VPN. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>HSM con connettività Direct Express Route
![Diagramma che mostra HSM con connessione diretta a Express Route](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti](faq.md)
- [Supporto](supportability.md)
- [Disponibilità elevata](high-availability.md)
- [Sicurezza fisica](physical-security.md)
- [Monitoring](monitoring.md)
- [Architettura di distribuzione](deployment-architecture.md)
