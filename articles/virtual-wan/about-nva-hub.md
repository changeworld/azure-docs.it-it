---
title: "rete WAN virtuale di Azure: Informazioni sull'appliance virtuale di rete nell'hub"
description: Questo articolo illustra le appliance virtuali di rete nell'hub della rete WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: 7c3ae14cd409e7bfc9be77c1a593964b73a12ddc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791050"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Informazioni sull'appliance virtuale di rete in un hub rete WAN virtuale di Azure (anteprima)

rete WAN virtuale di Azure ha collaborato con i partner di rete per creare un'automazione che semplifica la connessione delle apparecchiature locali del cliente (CPE) a un gateway VPN di Azure nell'hub virtuale. Azure collabora con partner di rete selezionati per consentire ai clienti di distribuire un'appliance virtuale di rete di terze parti direttamente nell'hub virtuale. In questo modo, i clienti che vogliono connettere il proprio CPE del ramo alla stessa NVA del marchio nell'hub virtuale possono sfruttare le funzionalità SD-WAN end-to-end proprietarie.

Barracuda Networks e Cisco Systems sono i primi partner a fornire le appliance virtuali di rete che possono essere distribuite direttamente nell'hub della rete WAN virtuale.  Vedere Barracuda CloudGen WAN , Cisco Cloud OnRamp for Multi-Cloud and [VMware SD-WAN](https://kb.vmware.com/s/article/82746) [(Barracuda CloudGen WAN),](https://www.barracuda.com/products/cloudgenwan) [Cisco Cloud OnRamp per multi-cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) e VMware SD-WAN per la documentazione del rispettivo prodotto. Azure sta collaborando con altri partner, quindi si prevede che seguiranno altre offerte.

> [!NOTE]
> Solo le offerte di NVA disponibili per la distribuzione nell'hub della rete WAN virtuale possono essere distribuite nell'hub della rete WAN virtuale. Non possono essere distribuiti in una rete virtuale arbitraria in Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Come funziona?

Le appliance virtuali di rete disponibili per la distribuzione diretta nell'hub rete WAN virtuale di Azure sono progettate specificamente per l'uso nell'hub virtuale. L'offerta dell'NVA viene pubblicata in Azure Marketplace come applicazione gestita e i clienti possono distribuire l'offerta direttamente da Azure Marketplace oppure dall'hub virtuale tramite l'portale di Azure.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Panoramica del processo":::

L'offerta dell'NVA di ogni partner avrà un'esperienza e funzionalità leggermente diverse in base ai requisiti di distribuzione. Esistono tuttavia alcuni aspetti comuni tra tutte le offerte dei partner per l'NVA nell'hub della rete WAN virtuale.

* Un'esperienza di applicazione gestita offerta tramite Azure Marketplace.
* Capacità e fatturazione basate su unità di infrastruttura dell'infrastruttura di rete.
* Le metriche di integrità sono e Monitoraggio di Azure.

### <a name="managed-application"></a><a name="managed"></a>Applicazione gestita

Tutte le offerte di macchine virtuali di rete disponibili per  la distribuzione nell'hub della rete WAN virtuale avranno un'applicazione gestita disponibile in Azure Marketplace. Le applicazioni gestite consentono ai partner di eseguire le operazioni seguenti:

* Creare un'esperienza di distribuzione personalizzata per l'NVA.
* Fornire un modello Resource Manager specifico che consenta loro di creare l'NVA direttamente nell'hub della rete WAN virtuale.
* Fatturare i costi delle licenze software direttamente o tramite Azure Marketplace.
* Esporre proprietà personalizzate e contatori delle risorse.

I partner di appliance virtuali di rete possono creare risorse diverse a seconda della distribuzione dell'appliance, delle licenze di configurazione e delle esigenze di gestione. Quando un cliente crea un'NVA nell'hub della rete WAN virtuale, come tutte le applicazioni gestite, nella sottoscrizione verranno creati due gruppi di risorse.

* **Gruppo di risorse del cliente:** conterrà un segnaposto dell'applicazione per l'applicazione gestita. I partner possono usarlo per esporre le proprietà dei clienti che scelgono qui.
* **Gruppo di risorse gestite:** i clienti non possono configurare o modificare direttamente le risorse in questo gruppo di risorse, perché questo è controllato dall'autore dell'applicazione gestita. Questo gruppo di risorse conterrà **la risorsa NetworkVirtualAppliances.**

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Gruppi di risorse dell'applicazione gestita":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Unità di infrastruttura NVA

Quando si crea un'infrastruttura di rete virtuale nell'hub della rete WAN virtuale, è necessario scegliere il numero di unità di infrastruttura NVA con cui si vuole distribuirla. **Un'unità di infrastruttura NVA** è un'unità di capacità di larghezza di banda aggregata per un'infrastruttura di rete nell'hub della rete WAN virtuale. **Un'unità di infrastruttura NVA** è simile a un'unità [](pricing-concepts.md#scale-unit) di scala VPN in termini di capacità e dimensionamento.

* 1 Unità di infrastruttura NVA rappresenta 500 Mbps di larghezza di banda aggregata per tutte le connessioni del sito di succursale in arrivo in questa NVA, al costo di $ 0,25/ora.
* Azure supporta da 1-80 unità di infrastruttura NVA per una determinata distribuzione dell'hub virtuale NVA.
* Ogni partner può offrire bundle di unità di infrastruttura di NVA diversi che sono un subset di tutte le configurazioni di unità di infrastruttura NVA supportate.

Analogamente alle unità di scala VPN, se si sceglie 1 unità di infrastruttura *NVA = 500 Mbps,* verranno create due istanze per la ridondanza, ognuna con una velocità effettiva massima di 500 Mbps. Se, ad esempio, si hanno cinque rami, ognuno dei quali contribuisce per 10 Mbps, sarà necessaria una velocità aggregata di 50 Mbps all'estremità finale. La pianificazione della capacità aggregata dell'NVA deve essere eseguita dopo aver valutato la capacità necessaria per supportare il numero di rami all'hub.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Processo di configurazione dell'appliance virtuale di rete

I partner hanno lavorato per offrire un'esperienza che configura automaticamente l'NVA come parte del processo di distribuzione. Dopo aver effettuato il provisioning dell'NVA nell'hub virtuale, qualsiasi configurazione aggiuntiva che potrebbe essere necessaria per l'NVA deve essere eseguita tramite il portale dei partner dell'NVA o l'applicazione di gestione. L'accesso diretto all'NVA non è disponibile.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Risorse del sito e della connessione con appliance di rete

A differenza delle configurazioni del gateway  VPN di Azure, non è  necessario creare risorse del **sito,** risorse di connessione da sito a sito o risorse di connessione da punto a sito per connettere i siti di succursale all'NVA nell'hub della rete WAN virtuale. Tutto questo viene gestito tramite il partner dell'NVA.

È comunque necessario creare connessioni da hub a rete virtuale per connettere l'hub della rete WAN virtuale alle reti virtuali di Azure.

## <a name="supported-regions"></a><a name="regions"></a>Aree supportate

L'NVA nell'hub virtuale è disponibile per l'anteprima nelle aree seguenti:

|Area geopolitica | Aree di Azure|
|---|---|
| America del Nord| Canada centrale, Canada orientale, Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-sette orientali, Stati Uniti centro-occidentali, Stati Uniti occidentali, Stati Uniti occidentali 2 |
| America del Sud | Brasile meridionale, Brasile sud-orientale |
| Europa | Francia centrale, Francia meridionale, Germania settentrionale, Germania centro-occidentale, Europa settentrionale, Norvegia orientale, Norvegia occidentale, Svizzera settentrionale, Svizzera occidentale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale|
|  Medio Oriente | Emirati Arabi Uniti settentrionali |
| Asia |  Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Asia sud-orientale | 
| Australia | Australia sud-orientale, Australia orientale, Australia centrale, Australia centrale 2|
| Africa | Sudafrica settentrionale |
| India | India meridionale, India occidentale, India centrale | 
## <a name="faq"></a>Domande frequenti

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Sono un partner di appliance di rete e voglio inserire l'appliance virtuale di rete nell'hub.  È possibile partecipare a questo programma per i partner?

Sfortunatamente, al momento non è possibile effettuare l'on board di nuove offerte partner. Torna a casa a novembre.

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>È possibile distribuire qualsiasi macchina virtuale di rete Azure Marketplace'hub della rete WAN virtuale?

Al momento, solo l'applicazione[Cisco Cloud vWAN](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) della rete [WAN Barracuda CloudGen](https://aka.ms/BarracudaMarketPlaceOffer)e [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) sono disponibili per la distribuzione nell'hub della rete WAN virtuale.  

### <a name="what-is-the-cost-of-the-nva"></a>Qual è il costo dell'NVA?

È necessario acquistare una licenza per l'NVA dal fornitore dell'NVA.  Per la licenza Barracuda CloudGen WAN NVA di Barracuda, vedere la pagina [DELLA RETE WAN CloudGen di Barracuda.](https://www.barracuda.com/products/cloudgenwan) Attualmente Cisco offre solo il modello di licenza BYOL (Bring Your Own License) che deve essere procurato direttamente da Cisco. Verranno inoltre sostenuti addebiti da Microsoft per le unità di infrastruttura NVA usate e per tutte le altre risorse usate. Per altre informazioni, vedere Concetti [relativi ai prezzi.](pricing-concepts.md)

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>È possibile distribuire un'istanza di NVA in un hub Basic?

No. Se si vuole distribuire un'istanza di NVA, è necessario usare un hub Standard.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>È possibile distribuire un'istanza di NVA in un hub sicuro?

Sì. Le macchine virtuali di rete partner possono essere distribuite in un hub con Firewall di Azure.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>È possibile connettere qualsiasi dispositivo CPE nella succursale a barracuda CloudGen WAN NVA nell'hub?

No. La rete WAN Barracuda CloudGen è compatibile solo con i dispositivi Barracuda CPE. Per altre informazioni sui requisiti della rete WAN CloudGen, vedere la pagina [della rete WAN CloudGen di Barracuda.](https://www.barracuda.com/products/cloudgenwan) Per Cisco sono disponibili diversi dispositivi CPE SD-WAN. Vedere Cisco Cloud OnRamp for Multi-Cloud documenation for compatable CPEs [(Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) documenation for compatable CPEs).

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Quali scenari di routing sono supportati con l'NVA nell'hub?

Tutti gli scenari di routing supportati dalla rete WAN virtuale sono supportati con appliance virtuali di rete nell'hub.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere [l'articolo Panoramica della rete WAN](virtual-wan-about.md) virtuale.
