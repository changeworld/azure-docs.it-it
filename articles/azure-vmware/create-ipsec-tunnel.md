---
title: Creare un tunnel IPSec in una soluzione VMware di Azure
description: Informazioni su come stabilire un tunnel da sito a sito VPN (IPsec IKEv1 e IKEv2) in soluzioni VMware di Azure.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027001"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Creare un tunnel IPSec in una soluzione VMware di Azure

In questo articolo verranno illustrati i passaggi per stabilire un tunnel VPN da sito a sito VPN (IPsec IKEv1 e IKEv2) che termina nel Microsoft Azure Hub WAN virtuale. L'hub contiene il gateway ExpressRoute della soluzione VMware di Azure e il gateway VPN da sito a sito. Connette un dispositivo VPN locale con un endpoint della soluzione VMware di Azure.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagramma che illustra l'architettura del tunnel VPN da sito a sito." border="false":::

In questa procedura:
- Creare un hub WAN virtuale di Azure e un gateway VPN con un indirizzo IP pubblico collegato. 
- Creare un gateway Azure ExpressRoute e stabilire un endpoint della soluzione VMware di Azure. 
- Abilitare la configurazione locale di una VPN basata su criteri. 

## <a name="prerequisites"></a>Prerequisiti
È necessario avere un indirizzo IP pubblico che termina in un dispositivo VPN locale.

## <a name="step-1-create-an-azure-virtual-wan"></a>Passaggio 1. Creare una rete WAN virtuale di Azure

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Passaggio 2: Creare un hub WAN virtuale e un gateway

>[!TIP]
>È anche possibile [creare un gateway in un hub esistente](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Selezionare la rete WAN virtuale creata nel passaggio precedente.

1. Selezionare **Crea hub virtuale**, immettere i campi necessari e quindi selezionare **Avanti: da sito a sito**. 

   Immettere la subnet usando un `/24` (minimo).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Screenshot che mostra la pagina Crea hub virtuale.":::

4. Selezionare la scheda **da sito a** sito, definire il gateway da sito a sito impostando la velocità effettiva aggregata dall'elenco a discesa **unità di scala gateway** . 

   >[!TIP]
   >Le unità di scala sono in coppie per la ridondanza, ciascuna delle quali supporta 500 Mbps (un'unità di scala = 500 Mbps). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Screenshot che mostra i dettagli da sito a sito.":::

5. Selezionare la scheda **ExpressRoute** , quindi creare un gateway ExpressRoute. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Screenshot delle impostazioni di ExpressRoute.":::

   >[!TIP]
   >Il valore di un'unità di scala è di 2 Gbps. 

    Sono necessari circa 30 minuti per creare ogni hub. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Passaggio 3. Creare una VPN da sito a sito

1. Nella portale di Azure selezionare la rete WAN virtuale creata in precedenza.

2. Nella **Panoramica** dell'hub virtuale selezionare VPN **connettività**  >  **(da sito a sito)**  >  **Crea nuovo sito VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Screenshot della pagina di panoramica per l'hub virtuale, con VPN (da sito a sito) e la creazione di un nuovo sito VPN selezionato.":::  
 
3. Nella scheda **nozioni di base** immettere i campi necessari. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Screenshot della scheda nozioni di base per il nuovo sito VPN.":::  

   1. Impostare l' **Border Gateway Protocol** per **abilitare**.  Se abilitata, garantisce che la soluzione VMware di Azure e i server locali annuncino le proprie route nel tunnel. Se disabilitata, le subnet che devono essere annunciate devono essere gestite manualmente. Se le subnet non vengono perse, HCX non riuscirà a formare la mesh del servizio. Per altre informazioni, vedere  [informazioni su BGP con il gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. Per lo **spazio di indirizzi privato**, immettere il blocco CIDR locale. Viene usato per indirizzare tutto il traffico associato per l'ambiente locale attraverso il tunnel. Il blocco CIDR è necessario solo se non si Abilita BGP.

1. Selezionare **Avanti: collegamenti** e completare i campi necessari. La specifica dei nomi di collegamento e provider consente di distinguere tra un numero qualsiasi di gateway che può essere creato come parte dell'hub. BGP e il numero di sistema autonomo (ASN) devono essere univoci all'interno dell'organizzazione.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Screenshot che mostra i dettagli del collegamento.":::

1. Selezionare **Rivedi e crea**. 

1. Passare all'hub virtuale desiderato e deselezionare **associazione hub** per connettere il sito VPN all'hub.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Screenshot che mostra il riquadro siti connessi per l'HUB virtuale pronto per chiave precondivisa e le impostazioni associate.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Passaggio 4. Opzionale Creare tunnel da sito a sito VPN basati su criteri

>[!IMPORTANT]
>Si tratta di un passaggio facoltativo che si applica solo alle VPN basate su criteri. 

Per le configurazioni VPN basate su criteri è necessario specificare reti locali e della soluzione VMware di Azure, inclusi gli intervalli di hub.  Questi intervalli di hub specificano il dominio di crittografia dell'endpoint locale del tunnel VPN basato su criteri.  Il lato della soluzione VMware di Azure richiede l'abilitazione dell'indicatore del selettore di traffico basato su criteri. 

1. Nella portale di Azure accedere al sito dell'hub WAN virtuale. In **connettività** selezionare **VPN (da sito a sito)**.

2. Selezionare il nome del sito VPN, i puntini di sospensione (...) all'estrema destra, quindi **modificare la connessione VPN a questo hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Screenshot della pagina in Azure per il sito hub WAN virtuale che mostra i puntini di sospensione selezionati per accedere alla modifica della connessione VPN a questo hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Modificare la connessione tra il sito VPN e l'hub e quindi selezionare **Save (Salva**).
   - IPSec (Internet Protocol Security) selezionare **personalizzato**.
   - Usare il selettore di traffico basato su criteri, selezionare **Abilita**
   - Specificare i dettagli per **IKE fase 1** e **IKE fase 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Screenshot della pagina Edit VPN connection."::: 
 
   I selettori di traffico o le subnet che fanno parte del dominio di crittografia basato su criteri devono essere:
    
   - Hub WAN virtuale `/24`
   - Cloud privato della soluzione VMware di Azure `/22`
   - Rete virtuale di Azure connessa (se presente)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Passaggio 5. Connettere il sito VPN all'hub

1. Selezionare il nome del sito VPN e quindi selezionare **Connetti siti VPN**. 

1. Nel campo **chiave precondivisa** immettere la chiave definita in precedenza per l'endpoint locale. 

   >[!TIP]
   >Se non è presente una chiave definita in precedenza, è possibile lasciare vuoto questo campo. Viene generata automaticamente una chiave. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Screenshot che mostra il riquadro siti connessi per l'HUB virtuale pronto per un chiave precondivisa e le impostazioni associate. "::: 

1. Se si sta distribuendo un firewall nell'hub ed è l'hop successivo, impostare l'opzione **propaga la route predefinita** su **Abilita**. 

   Se abilitata, l'hub WAN virtuale viene propagato a una connessione solo se l'hub ha già acquisito la route predefinita quando si distribuisce un firewall nell'hub o se è abilitato il tunneling forzato in un altro sito connesso. La route predefinita non ha origine nell'hub della rete WAN virtuale.  

1. Selezionare **Connetti**. Dopo alcuni minuti, il sito Visualizza lo stato di connessione e connettività.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Screenshot che mostra una connessione da sito a sito e lo stato di connettività." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Scaricare il file di configurazione VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#device) per l'endpoint locale.  

3. Applicare una patch alla soluzione VMware di Azure ExpressRoute nell'hub WAN virtuale. 

   >[!IMPORTANT]
   >Prima di poter applicare patch alla piattaforma, è necessario prima creare un cloud privato. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Collegare la soluzione VMware di Azure e il gateway VPN nell'hub WAN virtuale. Si userà la chiave di autorizzazione e l'ID ExpressRoute (URI del circuito peer) del passaggio precedente.

   1. Selezionare il gateway ExpressRoute e quindi fare clic su **riscatta chiave di autorizzazione**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Screenshot della pagina ExpressRoute per il cloud privato con la chiave di autorizzazione riscatto selezionata.":::

   1. Incollare la chiave di autorizzazione nel campo **chiave di autorizzazione** .
   1. Incollare l'ID ExpressRoute nel campo **URI del circuito peer** . 
   1. Selezionare **associa automaticamente questo circuito ExpressRoute con l'hub casella di** controllo. 
   1. Selezionare **Aggiungi** per stabilire il collegamento. 

5. Testare la connessione [creando un segmento NSX-T](./tutorial-nsx-t-network-segment.md) ed eseguendo il provisioning di una macchina virtuale nella rete. Effettuare il ping degli endpoint della soluzione VMware locale e di Azure.
