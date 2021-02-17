---
title: Esercitazione - Associare gli ambienti locali a un cloud privato
description: Informazioni su come creare il peering di Copertura globale ExpressRoute a un cloud privato in una soluzione Azure VMware.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558809"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Esercitazione: Associare gli ambienti locali a un cloud privato

Copertura globale ExpressRoute connette l'ambiente locale al cloud privato della soluzione Azure VMware. La connessione Copertura globale ExpressRoute viene stabilita tra un circuito ExpressRoute del cloud privato e una connessione ExpressRoute esistente agli ambienti locali. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Usare il portale di Azure per abilitare il peering del cloud da locale a privato ExpressRoute Copertura globale.


## <a name="before-you-begin"></a>Prima di iniziare

Prima di abilitare la connettività tra due circuiti ExpressRoute usando Copertura globale ExpressRoute, vedere la documentazione su come [abilitare la connettività in sottoscrizioni di Azure diverse](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Prerequisiti

- Circuito ExpressRoute separato e funzionante usato per connettere gli ambienti locali ad Azure.
- Assicurarsi che tutti i gateway, incluso il servizio del provider ExpressRoute, supportino il numero di sistema autonomo (ASN) a 4 byte. La soluzione VMware di Azure usa ASN pubblici a 4 byte per le route pubblicitarie.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Creare una chiave di autorizzazione ExpressRoute nel circuito ExpressRoute locale

1. Dal pannello **circuiti ExpressRoute** , in impostazioni, selezionare **autorizzazioni**.

2. Immettere il nome della chiave di autorizzazione e selezionare **Salva**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Selezionare autorizzazioni e immettere il nome della chiave di autorizzazione.":::
  
     Una volta creata, la nuova chiave viene visualizzata nell'elenco delle chiavi di autorizzazione per il circuito.
 
 4. Prendere nota della chiave di autorizzazione e dell'ID ExpressRoute. Verranno usati nel passaggio successivo per completare il peering.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Peering di cloud privato in locale

1. Nella scheda **Panoramica** del cloud privato in Gestisci selezionare **Connettività > Copertura globale ExpressRoute > Aggiungi**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Dal menu selezionare Connettività, Copertura globale ExpressRoute e quindi Aggiungi.":::

2. Immettere l'ID ExpressRoute e la chiave di autorizzazione creati nella sezione precedente.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Immettere l'ID ExpressRoute e la chiave di autorizzazione e quindi selezionare Crea.":::

3. Selezionare **Crea**. La nuova connessione viene visualizzata nell'elenco di connessioni cloud locali.  

>[!TIP]
>È possibile eliminare o disconnettere una connessione dall'elenco selezionando **Altro**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Disconnettere o eliminare una connessione locale":::


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come abilitare il peering Copertura globale cloud da locale a privato ExpressRoute. 

Per informazioni su come distribuire e configurare una soluzione VMware HCX per il cloud privato della soluzione Azure VMware, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Distribuire e configurare VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
