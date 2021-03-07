---
title: 'Azure ExpressRoute: configurare Copertura globale usando il portale di Azure'
description: Questo articolo consente di collegare i circuiti ExpressRoute per creare una rete privata tra le reti locali e abilitare Copertura globale usando il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431316"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Configurare ExpressRoute Copertura globale usando il portale di Azure

Questo articolo illustra come configurare Copertura globale di ExpressRoute con PowerShell. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare i criteri seguenti:

* Si conoscono i [flussi di lavoro del](expressroute-workflows.md)provisioning del circuito ExpressRoute.
* I circuiti ExpressRoute sono in stato di provisioning.
* Il peering privato di Azure viene configurato nei circuiti ExpressRoute.
* Se si vuole eseguire PowerShell localmente, verificare che nel computer sia installata la versione più recente di Azure PowerShell.

## <a name="identify-circuits"></a>Identificazione circuiti

1. In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

2. Identificare i circuiti ExpressRoute che si vuole usare. È possibile abilitare ExpressRoute Copertura globale tra il peering privato di due circuiti ExpressRoute, purché si trovino nei paesi/aree geografiche supportate. È necessario creare i circuiti in posizioni di peering diverse. 

   * Se la sottoscrizione è proprietaria di entrambi i circuiti, è possibile scegliere uno dei due circuiti per l'esecuzione della configurazione nelle sezioni seguenti.
   * Se i due circuiti si trovano in sottoscrizioni di Azure diverse, è necessaria l'autorizzazione di una delle due sottoscrizioni. Occorre quindi passare la chiave di autorizzazione quando si esegue il comando di configurazione nell'altra sottoscrizione di Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Screenshot dell'elenco dei circuiti ExpressRoute.":::

## <a name="enable-connectivity"></a>Abilitare la connettività

Abilitare la connettività tra le reti locali. Sono disponibili set distinti di istruzioni per i circuiti che si trovano nella stessa sottoscrizione di Azure e circuiti che sono sottoscrizioni diverse.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuiti ExpressRoute nella stessa sottoscrizione di Azure

1. Selezionare la configurazione del peering **privato di Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Screenshot della pagina Panoramica di ExpressRoute.":::

1. Selezionare **aggiungi copertura globale** per aprire la pagina *Aggiungi copertura globale* di configurazione.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Abilitare la copertura globale dal peering privato":::

1. Nella pagina *aggiungi copertura globale* configurazione assegnare un nome a questa configurazione. Selezionare il *circuito ExpressRoute* a cui si vuole connettere questo circuito e immettere in un **IPv4/29** per la *subnet copertura globale*. Gli indirizzi IP di questa subnet vengono usati per stabilire la connettività tra i due circuiti ExpressRoute. Non usare gli indirizzi in questa subnet nelle reti virtuali di Azure o nella rete locale. Selezionare **Aggiungi** per aggiungere il circuito alla configurazione del peering privato.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Screenshot dell'aggiunta di Copertura globale nel peering privato.":::

1. Selezionare **Save (Salva** ) per completare la configurazione del copertura globale. Al termine dell'operazione, si disporrà di connettività tra le due reti locali tramite entrambi i circuiti ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Screenshot del salvataggio delle configurazioni del peering privato.":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono nella stessa sottoscrizione di Azure, sarà necessaria l'autorizzazione. Nella configurazione seguente, l'autorizzazione viene generata dalla sottoscrizione del circuito 2. La chiave di autorizzazione viene quindi passata al circuito 1.

1. Generare una chiave di autorizzazione.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Screenshot della generazione della chiave di autorizzazione."::: 

   Prendere nota dell'ID risorsa del circuito 2 e della chiave di autorizzazione.

1. Selezionare la configurazione del peering **privato di Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Screenshot del peering privato nella pagina di panoramica.":::

1. Selezionare **aggiungi copertura globale** per aprire la pagina *Aggiungi copertura globale* di configurazione.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Screenshot dell'aggiunta di Copertura globale nel peering privato.":::

1. Nella pagina *aggiungi copertura globale* configurazione assegnare un nome a questa configurazione. Selezionare la casella **riscatta autorizzazione** . Immettere la **chiave di autorizzazione** e l' **ID del circuito ExpressRoute** generati e ottenuti nel passaggio 1. Fornire quindi un **IPv4/29** per la *subnet copertura globale*. Gli indirizzi IP di questa subnet vengono usati per stabilire la connettività tra i due circuiti ExpressRoute. Non usare gli indirizzi in questa subnet nelle reti virtuali di Azure o nella rete locale. Selezionare **Aggiungi** per aggiungere il circuito alla configurazione del peering privato.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Screenshot dell'aggiunta di Copertura globale con la chiave di autorizzazione.":::

1. Selezionare **Save (Salva** ) per completare la configurazione del copertura globale. Al termine dell'operazione, si disporrà di connettività tra le due reti locali tramite entrambi i circuiti ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Screenshot del salvataggio della configurazione del peering privato con Copertura globale.":::

## <a name="verify-the-configuration"></a>Verificare la configurazione

Verificare la configurazione del Copertura globale selezionando il *peering privato* nella configurazione del circuito ExpressRoute. Una volta configurata correttamente, la configurazione deve essere simile a quanto segue:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Screenshot della Copertura globale configurata.":::

## <a name="disable-connectivity"></a>Disabilitare la connettività

Per disabilitare la connettività tra un singolo circuito, selezionare il pulsante Elimina accanto al *nome del copertura globale* per rimuovere la connettività tra di essi. Quindi selezionare **Save (Salva** ) per completare l'operazione.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Screenshot che illustra come disabilitare Copertura globale.":::

Al termine dell'operazione, non sarà più disponibile una connettività tra la rete locale e i circuiti ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni su Copertura globale di ExpressRoute](expressroute-global-reach.md)
- [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
- [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)
