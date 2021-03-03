---
title: 'Azure ExpressRoute: aggiungere il supporto IPv6 usando il portale di Azure'
description: Informazioni su come aggiungere il supporto IPv6 per connettersi alle distribuzioni di Azure usando il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 67f296c7584fcf25af79f9125137aca07c9906fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746083"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Aggiungere il supporto IPv6 per il peering privato usando il portale di Azure (anteprima)

Questo articolo descrive come aggiungere il supporto IPv6 per la connessione tramite ExpressRoute alle risorse in Azure usando il portale di Azure. 

> [!Note]
> Questa funzionalità è attualmente disponibile per l'anteprima nelle [aree di Azure con zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). È quindi possibile creare il circuito ExpressRoute usando qualsiasi località di peering, ma le distribuzioni basate su IPv6 a cui si connette devono trovarsi in un'area con zone di disponibilità.

## <a name="register-for-public-preview"></a>Registrarsi per l'anteprima pubblica
Prima di aggiungere il supporto per IPv6, è necessario prima registrare la sottoscrizione. Per eseguire la registrazione, eseguire le operazioni seguenti tramite Azure PowerShell:
1.  Accedere ad Azure e selezionare la sottoscrizione. È necessario eseguire questa operazione per la sottoscrizione contenente il circuito ExpressRoute, nonché la sottoscrizione contenente le distribuzioni di Azure (se sono diverse).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registrare la sottoscrizione per l'anteprima pubblica usando il comando seguente:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

La richiesta verrà quindi approvata dal team di ExpressRoute entro 2-3 giorni lavorativi.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Da un browser passare alla [portale di Azure](https://portal.azure.com)e quindi accedere con l'account Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Aggiungere peering privato IPv6 al circuito ExpressRoute

1. [Creare un circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) o passare al circuito esistente che si vuole modificare.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Passare al circuito":::

2. Selezionare la configurazione del peering **privato di Azure** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Passare al peering":::

3. Aggiungere un peering privato IPv6 alla configurazione del peering privato IPv4 esistente selezionando "both" per le **subnet** o abilitando il peering privato IPv6 solo sul nuovo circuito selezionando "IPv6". Fornire una coppia di subnet IPv6/126 di cui si è proprietari per il collegamento primario e i collegamenti secondari. Da ognuna di queste subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft. **Salvare** la configurazione del peering dopo aver specificato tutti i parametri.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Aggiungere peering privato IPv6":::

4. Dopo che la configurazione è stata accettata correttamente, dovrebbe essere visualizzata una schermata simile all'esempio seguente.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Visualizzare il peering privato IPv6":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aggiornare la connessione a una rete virtuale esistente

Seguire questa procedura se si dispone di un ambiente esistente di risorse di Azure in un'area con zone di disponibilità a cui si vuole usare il peering privato IPv6 con.

1. Passare alla rete virtuale a cui è connesso il circuito ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Passare a VNET":::

2. Passare alla scheda **spazio indirizzi** e aggiungere uno spazio di indirizzi IPv6 alla rete virtuale. **Salvare** lo spazio di indirizzi.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Aggiungi spazio di indirizzi IPv6":::

3. Passare alla scheda **subnet** e selezionare il **GatewaySubnet**. Selezionare **Aggiungi spazio di indirizzi IPv6** e fornire uno spazio indirizzi IPv6 per la subnet. La subnet IPv6 del gateway deve essere/64 o superiore. **Salvare** la configurazione dopo aver specificato tutti i parametri.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Aggiungere lo spazio di indirizzi IPv6 alla subnet":::

4. Se si dispone di un gateway con ridondanza della zona esistente, passare al gateway ExpressRoute e aggiornare la risorsa per abilitare la connettività IPv6. In caso contrario, [creare il gateway di rete virtuale](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) usando uno SKU con ridondanza della zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se si prevede di usare FastPath, usare ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Aggiornare il gateway":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Creare una connessione a una nuova rete virtuale

Se si prevede di connettersi a un nuovo set di risorse di Azure in un'area con zone di disponibilità usando il peering privato IPv6, seguire questa procedura.

1. Creare una rete virtuale a doppio stack con lo spazio degli indirizzi IPv4 e IPv6. Per altre informazioni, vedere [creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Creare la subnet del gateway dual stack](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet).

3. [Creare il gateway di rete virtuale](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway) usando uno SKU con ridondanza della zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se si prevede di usare FastPath, usare ErGw3AZ.

4. [Collegare la rete virtuale al circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

## <a name="limitations"></a>Limitazioni
Sebbene il supporto per IPv6 sia disponibile per le connessioni alle distribuzioni in aree con zone di disponibilità, non supporta i casi d'uso seguenti:

* Connessioni alle distribuzioni in Azure tramite uno SKU del gateway non AZ ExpressRoute
* Connessioni alle distribuzioni in aree non AZ
* Copertura globale le connessioni tra circuiti ExpressRoute

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi relativi a ExpressRoute, vedere gli articoli seguenti:

* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)
