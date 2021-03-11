---
title: 'Azure ExpressRoute: aggiungere il supporto IPv6 usando il portale di Azure'
description: Informazioni su come aggiungere il supporto IPv6 per connettersi alle distribuzioni di Azure usando il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 9926102a2e6b25060c2a8840b56d690ce2868ade
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618795"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Aggiungere il supporto IPv6 per il peering privato usando il portale di Azure (anteprima)

Questo articolo descrive come aggiungere il supporto IPv6 per la connessione tramite ExpressRoute alle risorse in Azure usando il portale di Azure. 

> [!Note]
> Questa funzionalità è attualmente disponibile per l'anteprima nelle [aree di Azure con zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). È quindi possibile creare il circuito ExpressRoute usando qualsiasi località di peering, ma le distribuzioni basate su IPv6 a cui si connette devono trovarsi in un'area con zone di disponibilità.

## <a name="register-for-public-preview"></a>Registrarsi per l'anteprima pubblica
Prima di aggiungere il supporto per IPv6, è necessario prima registrare la sottoscrizione. Per eseguire la registrazione, eseguire i comandi seguenti tramite Azure PowerShell:

1.  Accedere ad Azure e selezionare la sottoscrizione. Eseguire questi comandi per la sottoscrizione contenente il circuito ExpressRoute e la sottoscrizione contenente le distribuzioni di Azure (se sono diverse).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registrare la sottoscrizione per l'anteprima pubblica usando il comando seguente:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

La richiesta verrà quindi approvata dal team di ExpressRoute entro 2-3 giorni lavorativi.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Da un browser passare alla [portale di Azure](https://portal.azure.com)e quindi accedere con l'account Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Aggiungere peering privato IPv6 al circuito ExpressRoute

1. [Creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) o passare al circuito esistente che si vuole modificare.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Screenshot dell'elenco di circuiti ExpressRoute.":::

1. Selezionare la configurazione del peering **privato di Azure** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Screenshot della pagina Panoramica di ExpressRoute.":::

1. Aggiungere un peering privato IPv6 alla configurazione del peering privato IPv4 esistente selezionando "both" per le **subnet** o abilitando il peering privato IPv6 solo sul nuovo circuito selezionando "IPv6". Fornire una coppia di subnet IPv6/126 di cui si è proprietari per il collegamento primario e i collegamenti secondari. Da ognuna di queste subnet si assegnerà il primo indirizzo IP utilizzabile al router, perché Microsoft usa il secondo IP utilizzabile per il router. **Salvare** la configurazione del peering dopo aver specificato tutti i parametri.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Screenshot dell'aggiunta di IPv6 nella pagina peering privato.":::

1. Dopo che la configurazione è stata accettata correttamente, dovrebbe essere visualizzata una schermata simile all'esempio seguente.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Screenshot di IPv6 configurato per il peering privato.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aggiornare la connessione a una rete virtuale esistente

Seguire questa procedura se si dispone di un ambiente esistente di risorse di Azure in un'area con zone di disponibilità che si vuole usare con il peering privato IPv6 con.

1. Passare alla rete virtuale a cui è connesso il circuito ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Screenshot dell'elenco di reti virtuali.":::

1. Passare alla scheda **spazio indirizzi** e aggiungere uno spazio di indirizzi IPv6 alla rete virtuale. **Salvare** lo spazio di indirizzi.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Screenshot della pagina Aggiungi spazio indirizzi IPv6 alla rete virtuale.":::

1. Passare alla scheda **subnet** e selezionare il **GatewaySubnet**. Selezionare **Aggiungi spazio di indirizzi IPv6** e fornire uno spazio indirizzi IPv6 per la subnet. La subnet IPv6 del gateway deve essere/64 o superiore. **Salvare** la configurazione dopo aver specificato tutti i parametri.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Screenshot della pagina Aggiungi spazio indirizzi IPv6 alla subnet.":::

1. Se si dispone di un gateway con ridondanza della zona esistente, passare al gateway ExpressRoute e aggiornare la risorsa per abilitare la connettività IPv6. In caso contrario, [creare il gateway di rete virtuale](expressroute-howto-add-gateway-portal-resource-manager.md) usando uno SKU con ridondanza della zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se si prevede di usare FastPath, usare ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Screenshot dell'aggiornamento del gateway.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Creare una connessione a una nuova rete virtuale

Se si prevede di connettersi a un nuovo set di risorse di Azure in un'area con zone di disponibilità usando il peering privato IPv6, seguire questa procedura.

1. Creare una rete virtuale a doppio stack con lo spazio degli indirizzi IPv4 e IPv6. Per altre informazioni, vedere [creare una rete virtuale](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Creare la subnet del gateway dual stack](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Creare il gateway di rete virtuale](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) usando uno SKU con ridondanza della zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se si prevede di usare FastPath, usare ErGw3AZ (si noti che questa opzione è disponibile solo per i circuiti che usano ExpressRoute Direct).

1. [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Limitazioni
Sebbene il supporto per IPv6 sia disponibile per le connessioni alle distribuzioni in aree con zone di disponibilità, non supporta i casi d'uso seguenti:

* Connessioni alle distribuzioni in Azure tramite uno SKU del gateway non AZ ExpressRoute
* Connessioni alle distribuzioni in aree non AZ
* Copertura globale le connessioni tra circuiti ExpressRoute
* Uso di ExpressRoute con la rete WAN virtuale
* FastPath con circuiti diretti non ExpressRoute
* Coesistenza con il gateway VPN

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi relativi a ExpressRoute, vedere gli articoli seguenti:

* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)
