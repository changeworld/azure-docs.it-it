---
title: 'Esercitazione: Connettere la rete locale alla rete virtuale: Portale di Azure'
description: Creare una connessione IPSec del gateway VPN da sito a sito dalla rete locale a una rete virtuale di Azure tramite Internet pubblico usando il portale.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945211"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Esercitazione: Creare una connessione da sito a sito nel portale di Azure

I gateway VPN di Azure offrono connettività cross-premise tra l'infrastruttura locale del cliente e Azure. Questa esercitazione illustra come usare il portale di Azure per creare una connessione del gateway VPN da sito a sito dalla rete locale alla rete virtuale. È anche possibile creare questa configurazione tramite [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) o l'[interfaccia della riga di comando di Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagramma della connessione cross-premise gateway VPN da sito a sito":::

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway VPN
> * Creare un gateway di rete locale
> * Creare una connessione a VPN
> * Verificare la connessione
> * Connettersi a una macchina virtuale

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non è disponibile, [crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Creare una rete virtuale

Creare una rete virtuale con i valori seguenti:

* **Gruppo di risorse:** TestRG1
* **Nome:** VNet1
* **Area:** (Stati Uniti) Stati Uniti orientali
* **Spazio indirizzi IPv4:** 10.1.0.0/16
* **Nome della subnet:** FrontEnd
* **Spazio indirizzi della subnet:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Creare un gateway VPN

Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

### <a name="about-the-gateway-subnet"></a>Informazioni sulla subnet del gateway

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Creare il gateway

Creare un gateway VPN con i valori seguenti:

* **Nome:** VNet1GW
* **Area:** Stati Uniti orientali
* **Tipo di gateway:** VPN
* **Tipo di VPN:** basato su route
* **SKU:** VpnGw1
* **Generazione:** Generation1
* **Rete virtuale:** VNet1
* **Intervallo di indirizzi subnet del gateway:** 10.1.255.0/27
* **Indirizzo IP pubblico:** Creare un nuovo gruppo di risorse
* **Nome indirizzo IP pubblico:** VNet1GWpip
* **Abilita modalità attiva-attiva:** Disabled
* **Configura BGP:** Disabled

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>Visualizzare l'indirizzo IP pubblico

È possibile visualizzare l'indirizzo IP pubblico del gateway nella pagina **Panoramica** del gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Pagina di panoramica":::

Per visualizzare altre informazioni sull'oggetto indirizzo IP pubblico, fare clic sul collegamento del nome/indirizzo IP accanto a **Indirizzo IP pubblico**.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Creare un gateway di rete locale

Il gateway di rete locale è un oggetto specifico che rappresenta la posizione locale (il sito) a scopo di routing. Assegnare al sito un nome che Azure possa usare come riferimento, quindi specificare l'indirizzo IP del dispositivo VPN locale con cui si creerà una connessione. Specificare anche i prefissi degli indirizzi IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale. Se la rete locale viene modificata o è necessario modificare l'indirizzo IP pubblico del dispositivo VPN, è possibile aggiornare facilmente i valori in un secondo momento.

Creare un gateway di rete locale con i valori seguenti:

* **Nome:** Site1
* **Gruppo di risorse:** TestRG1
* **Località:** Stati Uniti orientali

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Per la configurazione del dispositivo VPN è necessario specificare i valori seguenti:

* Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
* Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway VPN usando il portale di Azure, passare a **Gateway di rete virtuali**, quindi selezionare il nome del gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Creare una connessione a VPN

Creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale.

Creare una connessione con i valori seguenti:

* **Nome del gateway di rete locale:** Site1
* **Nome connessione**: VNet1toSite1
* **Chiave condivisa:** in questo esempio si usa abc123. È possibile usare qualsiasi valore compatibile con l'hardware VPN, ma è importante che i valori corrispondano su entrambi i lati della connessione.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Verificare la connessione VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Passaggi facoltativi

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Aggiungere altre connessioni al gateway

È possibile aggiungere altre connessioni a condizione che non si crei alcuna sovrapposizione degli spazi indirizzi tra le connessioni.

1. Per aggiungere un'altra connessione, passare al gateway VPN e quindi selezionare **Connessioni** per aprire la pagina Connessioni.
1. Selezionare **+Aggiungi** per aggiungere la connessione. Modificare il tipo di connessione per riflettere una connessione da rete virtuale a rete virtuale (in caso di connessione a un altro gateway di rete virtuale) o da sito a sito.
1. Se si usa una connessione da sito a sito e non si è ancora creato un gateway di rete locale per il sito a cui ci si vuole connettere, è possibile crearne uno nuovo.
1. Specificare la chiave condivisa da usare e quindi selezionare **OK** per creare la connessione.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ridimensionare uno SKU del gateway

Il ridimensionamento e la modifica di uno SKU del gateway sono soggetti a regole specifiche. In questa sezione si ridimensionerà lo SKU. Per altre informazioni, vedere [Impostazioni del gateway - Ridimensionare o modificare uno SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Reimpostare un gateway

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Altre considerazioni sulla configurazione

Le configurazioni da sito a sito possono essere personalizzate in vari modi. Per altre informazioni, vedere gli articoli seguenti:

* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Per informazioni sul tunneling forzato, vedere [Informazioni sul tunneling forzato](vpn-gateway-forced-tunneling-rm.md).
* Per informazioni sulle connessioni da rete attiva a rete attiva a disponibilità elevata, vedere [Connettività cross-premise e da rete virtuale a rete virtuale a disponibilità elevata](vpn-gateway-highlyavailable.md).
* Per informazioni su come limitare il traffico di rete verso le risorse in una rete virtuale, vedere [Sicurezza di rete](../virtual-network/network-security-groups-overview.md).
* Per informazioni sul modo in cui Azure instrada il traffico tra Azure, l'ambiente locale e le risorse Internet, vedere [Routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di continuare a usare questa applicazione o di passare all'esercitazione successiva, eliminare le risorse seguendo questa procedura:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale e selezionarlo nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato la connessione da sito a sito, è possibile aggiungere una connessione da punto a sito allo stesso gateway.

> [!div class="nextstepaction"]
> [Connessioni VPN da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
