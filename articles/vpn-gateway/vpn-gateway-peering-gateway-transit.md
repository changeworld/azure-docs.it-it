---
title: Configurare il transito nel gateway VPN per il peering di rete virtuale
description: Configurare il transito del gateway per il peering di rete virtuale per connettere in modo uniforme due reti virtuali di Azure a uno per finalità di connettività.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872180"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurare il transito nel gateway VPN per il peering di rete virtuale

Questo articolo aiuta a configurare il transito nel gateway per il peering di rete virtuale. Il [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md) connette facilmente due reti virtuali di Azure, unendole in un'unica rete per scopi di connettività. Il [transito del gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) è una proprietà di peering che consente a una rete virtuale di usare il gateway VPN nella rete virtuale con peering per la connettività cross-premise o da VNet a VNet. Il diagramma seguente mostra come funziona il transito nel gateway con il peering di rete virtuale.

![Diagramma di transito del gateway](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Nel diagramma il transito nel gateway consente alle reti virtuali con peering di usare il gateway VPN di Azure nella rete Hub-RM. La connettività disponibile nel gateway VPN, incluse le connessioni S2S, P2S e da rete virtuale a rete virtuale, si applica a tutte e tre le reti virtuali. L'opzione di transito è disponibile per il peering tra lo stesso o modelli di distribuzione diversi. Se si configura il transito tra modelli di distribuzione diversi, la rete virtuale dell'hub e il gateway di rete virtuale devono trovarsi nel modello di distribuzione Gestione risorse e non nel modello di distribuzione classica.
>

Nell'architettura di rete hub-spoke il transito nel gateway consente alle reti virtuali spoke di condividere il gateway VPN nell'hub invece di distribuire gateway VPN in ogni rete virtuale spoke. Le route alle reti virtuali connesse al gateway o alle reti locali si propagheranno alle tabelle di routing per le reti virtuali con peering che usano il transito nel gateway. È possibile disabilitare la propagazione automatica della route dal gateway VPN. Creare una tabella di routing con l'opzione "**Disabilita propagazione route BGP**" e associare la tabella di routing alle subnet per impedire la distribuzione di route alle subnet. Per altre informazioni, vedere [Tabella di routing di una rete virtuale](../virtual-network/manage-route-table.md).

Questo articolo presenta due scenari:

* **Stesso modello di distribuzione**: vengono create entrambe le reti virtuali nel modello di distribuzione gestione risorse.
* **Modelli di distribuzione diversi**: la rete virtuale spoke viene creata nel modello di distribuzione classica e la rete virtuale e il gateway dell'hub si trovano nel modello di distribuzione gestione risorse.

>[!NOTE]
> Se si apporta una modifica alla topologia della rete e sono disponibili client VPN di Windows, il pacchetto client VPN per i client Windows deve essere scaricato e installato nuovamente affinché le modifiche vengano applicate al client.
>

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano disponibili le reti virtuali e le autorizzazioni seguenti:

### <a name="virtual-networks"></a><a name="vnet"></a>Reti virtuali

|VNet|Modello di distribuzione| Gateway di rete virtuale|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Sì](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| No |
| Spoke-Classic | [Classico](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | No |

### <a name="permissions"></a><a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessari. Nell'esempio seguente, se si esegue il peering delle due reti virtuali denominate **Hub-RM** e **spoke-Classic**, l'account deve avere i ruoli o le autorizzazioni seguenti per ogni rete virtuale:

|VNet|Modello di distribuzione|Ruolo|Autorizzazioni|
|---|---|---|---|
|Hub-RM|Gestione risorse|[Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classico|[Collaboratore reti virtuali classiche](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|Spoke-Classic|Gestione risorse|[Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classico|[Collaboratore reti virtuali classiche](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Stesso modello di distribuzione

In questo scenario, le reti virtuali sono entrambe nel modello di distribuzione Gestione risorse. Usare la procedura seguente per creare o aggiornare i peering di rete virtuale per abilitare il transito del gateway.

### <a name="to-add-a-peering-and-enable-transit"></a>Per aggiungere un peering e abilitare il transito

1. Nel [portale di Azure](https://portal.azure.com)creare o aggiornare il peering di rete virtuale da Hub-RM. Passare alla rete virtuale **Hub-RM** . Selezionare **peering**, quindi **+ Aggiungi** per aprire **Aggiungi peering**.
1. Nella pagina **Aggiungi peering** configurare i valori per la **rete virtuale**.

   * Nome collegamento peering: assegnare un nome al collegamento. Esempio: **HubRMToSpokeRM**
   * Traffico verso una rete virtuale remota: **Consenti**
   * Traffico inviato dalla rete virtuale remota: **Consenti**
   * Gateway di rete virtuale: **usare il gateway di questa rete virtuale**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Screenshot che mostra l'aggiunta del peering.":::

1. Nella stessa pagina continuare con per configurare i valori per la **rete virtuale remota**.

   * Nome collegamento peering: assegnare un nome al collegamento. Esempio: **SpokeRMtoHubRM**
   * Modello di distribuzione: **Gestione risorse**
   * Rete virtuale: **spoke-RM**
   * Traffico verso una rete virtuale remota: **Consenti**
   * Traffico inviato dalla rete virtuale remota: **Consenti**
   * Gateway di rete virtuale: **usare il gateway della rete virtuale remota**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Screenshot mostra i valori per la rete virtuale remota.":::

1. Selezionare **Aggiungi** per creare il peering.
1. Verificare lo stato del peering come **connesso** in entrambe le reti virtuali.

### <a name="to-modify-an-existing-peering-for-transit"></a>Per modificare un peering esistente per il transito

Se il peering è già stato creato, è possibile modificare il peering per il transito.

1. Passare alla rete virtuale. Selezionare **peering** e selezionare il peering che si desidera modificare.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Screenshot mostra i peering selezionati.":::

1. Aggiornare il peering VNet.

   * Traffico verso una rete virtuale remota: **Consenti**
   * Traffico inviato alla rete virtuale; **Consenti**
   * Gateway di rete virtuale: **usare il gateway della rete virtuale remota**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Screenshot mostra modificare il gateway di peering.":::

1. **Salvare** le impostazioni del peering.

### <a name="powershell-sample"></a><a name="ps-same"></a>Esempio PowerShell

È inoltre possibile usare PowerShell per creare o aggiornare il peering con l'esempio precedente. Sostituire le variabili con i nomi delle reti virtuali e dei gruppi di risorse.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>Modelli di distribuzione diversi

In questa configurazione, spoke VNet **spoke-Classic** si trova nel modello di distribuzione classica e hub VNet Hub **-RM** si trova nel modello di distribuzione gestione risorse. Quando si configura il transito tra i modelli di distribuzione, il gateway di rete virtuale deve essere configurato per l'Gestione risorse VNet, non per la VNet classica.

Per questa configurazione, è sufficiente configurare la rete virtuale **Hub-RM** . Non è necessario configurare alcun elemento in VNet **spoke-Classic** .

1. Nella portale di Azure passare alla rete virtuale **Hub-RM** , selezionare **peering**, quindi selezionare **+ Aggiungi**.
1. Nella pagina **Aggiungi peering** configurare i valori seguenti:

   * Nome collegamento peering: assegnare un nome al collegamento. Esempio: **HubRMToClassic**
   * Traffico verso una rete virtuale remota: **Consenti**
   * Traffico inviato dalla rete virtuale remota: **Consenti**
   * Gateway di rete virtuale: **usare il gateway di questa rete virtuale**
   * Rete virtuale remota: **classica**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Pagina Aggiungi peering per spoke-classico":::

1. Verificare che la sottoscrizione sia corretta, quindi selezionare la rete virtuale nell'elenco a discesa.
1. Selezionare **Aggiungi** per aggiungere il peering.
1. Verificare lo stato del peering come **connesso** nella rete virtuale Hub-RM. 

Per questa configurazione non è necessario configurare alcun elemento nella rete virtuale **spoke-Classic** . Una volta visualizzato lo stato **connesso**, la rete virtuale spoke può usare la connettività tramite il gateway VPN nella rete virtuale dell'hub.

### <a name="powershell-sample"></a><a name="ps-different"></a>Esempio PowerShell

È inoltre possibile usare PowerShell per creare o aggiornare il peering con l'esempio precedente. Sostituire le variabili e l'ID sottoscrizione con i valori dei gruppi di risorse e della rete virtuale e con la sottoscrizione. È necessario creare solo il peering di rete virtuale nella rete virtuale hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Passaggi successivi

* Acquisire altre informazioni su [comportamenti e vincoli del peering di rete virtuale](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) e [impostazioni del peering di rete virtuale](../virtual-network/virtual-network-manage-peering.md#create-a-peering) prima di creare un peering di rete virtuale per l'uso in produzione.
* Acquisire informazioni su come [creare una topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) con peering di rete virtuale e transito nel gateway.
* [Creare un peering di rete virtuale con lo stesso modello di distribuzione](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Creare un peering di rete virtuale con modelli di distribuzione diversi](../virtual-network/create-peering-different-deployment-models.md).