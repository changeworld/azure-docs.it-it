---
title: Esercitazione - Associare gli ambienti locali a un cloud privato
description: Informazioni su come creare il peering di Copertura globale ExpressRoute a un cloud privato in una soluzione Azure VMware.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 08a461ab7d441eb813d435086dfcea121ef4a7f1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786907"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Esercitazione: Associare gli ambienti locali a un cloud privato

Copertura globale ExpressRoute connette l'ambiente locale al cloud privato della soluzione Azure VMware. La connessione Copertura globale ExpressRoute viene stabilita tra un circuito ExpressRoute del cloud privato e una connessione ExpressRoute esistente agli ambienti locali. 

Il circuito ExpressRoute usato quando si [Configura la rete per il cloud privato VMware in Azure](tutorial-configure-networking.md) richiede la creazione e l'uso delle chiavi di autorizzazione.  È già stata usata una chiave di autorizzazione dal circuito ExpressRoute. in questa esercitazione verrà creata una seconda chiave di autorizzazione per il peering con il circuito ExpressRoute locale.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una seconda chiave di autorizzazione per il _circuito 2_, il circuito ExpressRoute del cloud privato.
> * Usare il portale di Azure o l'interfaccia della riga di comando di Azure in un metodo di Cloud Shell nella sottoscrizione del _circuito 1_ per abilitare il peering di copertura globale ExpressRoute per il cloud da sito locale a privato.


## <a name="before-you-begin"></a>Prima di iniziare

Prima di abilitare la connettività tra due circuiti ExpressRoute usando Copertura globale ExpressRoute, vedere la documentazione su come [abilitare la connettività in sottoscrizioni di Azure diverse](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Prerequisiti

- Connettività stabilita verso e da un cloud privato della soluzione Azure VMware con peering del circuito ExpressRoute con un gateway ExpressRoute in una rete virtuale di Azure, ovvero circuito 2 delle procedure di peering.
- Un circuito ExpressRoute separato e funzionante usato per connettere gli ambienti locali ad Azure, ovvero il circuito 1 dal punto di vista delle procedure di peering.
- Un [blocco di indirizzi di rete](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) non sovrapposti /29 per il peering Copertura globale ExpressRoute.
- Assicurarsi che tutti i gateway, incluso il servizio del provider ExpressRoute, supportino il numero di sistema autonomo (ASN) a 4 byte. La soluzione VMware di Azure usa ASN pubblici a 4 byte per le route pubblicitarie.

>[!IMPORTANT]
>Nel contesto di questi prerequisiti, il circuito ExpressRoute locale è _circuito 1_ e il circuito ExpressRoute del cloud privato si trova in una sottoscrizione diversa ed è contrassegnato come _circuito 2_.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>Creare una chiave di autorizzazione ExpressRoute nel circuito ExpressRoute del cloud privato

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Peer da cloud privato a locale con chiave di autorizzazione
Ora che è stata creata una chiave di autorizzazione per il circuito ExpressRoute del cloud privato, è possibile associarla al circuito ExpressRoute locale. Il peering viene eseguito dal punto di vista del circuito ExpressRoute locale nel **portale di Azure** o tramite l'**interfaccia della riga di comando di Azure in un'istanza di Cloud Shell**. Con entrambi i metodi verranno usati l'ID risorsa e la chiave di autorizzazione del circuito ExpressRoute del cloud privato per completare il peering.

### <a name="portal"></a>[Portale](#tab/azure-portal)
 
1. Accedere al [portale di Azure](https://portal.azure.com) usando la stessa sottoscrizione del circuito ExpressRoute locale.

1. Nella **Panoramica** del cloud privato, in Gestisci selezionare **connettività**  >  **ExpressRoute copertura globale**  >  **Aggiungi**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Screenshot che mostra la scheda Copertura globale ExpressRoute nel cloud privato della soluzione VMware di Azure.":::

1. Creare una connessione cloud locale. Eseguire una delle operazioni seguenti e quindi selezionare **Crea**:

   - Selezionare il **circuito ExpressRoute** dall'elenco oppure
   - Se si dispone dell'ID circuito, incollarlo nel campo e specificare la chiave di autorizzazione.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Immettere l'ID ExpressRoute e la chiave di autorizzazione e quindi selezionare Crea.":::   
   
   La nuova connessione viene visualizzata nell'elenco di connessioni cloud locali.

>[!TIP]
>È possibile eliminare o disconnettere una connessione dall'elenco selezionando **Altro**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Disconnettere o eliminare una connessione locale":::

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I [comandi dell'interfaccia della riga di comando](../expressroute/expressroute-howto-set-global-reach-cli.md) sono stati ampliati con dettagli ed esempi specifici che consentono di configurare il peering Copertura globale ExpressRoute tra ambienti locali con un cloud privato della soluzione Azure VMware.

>[!TIP]
>Per brevità nell'output del comando dell'interfaccia della riga di comando di Azure, queste istruzioni possono usare un [ `–query` argomento](https://docs.microsoft.com/cli/azure/query-azure-cli) per eseguire una query JMESPath per visualizzare solo i risultati necessari.

1. Accedere al [portale di Azure](https://portal.azure.com) usando la stessa sottoscrizione del circuito ExpressRoute locale. 

1. Aprire una Cloud Shell e lasciare la shell come bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Screenshot che mostra l'Cloud Shell di portale di Azure.":::

1. Creare il peering sul circuito 1, passando l'ID risorsa e la chiave di autorizzazione del circuito 2. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Screenshot che mostra il comando e i risultati di un peering riuscito tra il circuito 1 e il circuito 2.":::

È possibile connettersi dagli ambienti locali al cloud privato tramite il peering Copertura globale ExpressRoute.

>[!TIP]
>Per eliminare il peering, seguire le istruzioni [Disabilita connettività tra le reti locali](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come abilitare il peering Copertura globale cloud da locale a privato ExpressRoute. 

Per informazioni su come distribuire e configurare una soluzione VMware HCX per il cloud privato della soluzione Azure VMware, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Distribuire e configurare VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
