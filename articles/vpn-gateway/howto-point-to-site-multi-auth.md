---
title: 'Connettersi a una VNet usando la VPN P2S & più tipi di autenticazione: portale'
titleSuffix: Azure VPN Gateway
description: Connettersi a una VNet tramite P2S usando più tipi di autenticazione nel portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745686"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Configurare una connessione VPN da punto a sito a una VNet usando più tipi di autenticazione: portale di Azure

Questo articolo consente di connettere in modo sicuro i singoli client che eseguono Windows, Linux o macOS a una VNet di Azure. Le connessioni VPN da punto a sito sono utili per connettersi alla rete virtuale da una posizione remota, ad esempio nel caso di telecomunicazioni da casa o durante una riunione. È anche possibile usare una VPN da punto a sito al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico. La modalità da punto a sito crea la connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol) o IKEv2. Per altre informazioni sulle connessioni VPN da punto a sito, vedere [Informazioni sulla VPN da punto a sito](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Connettersi da un computer a un diagramma di connessione da punto a sito di Azure VNet":::

Per ulteriori informazioni sulla VPN da punto a sito, vedere [informazioni sulla VPN da punto a sito](point-to-site-about.md). Per creare questa configurazione usando il Azure PowerShell, vedere [configurare una VPN da punto a sito con Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Prerequisiti

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

Più tipi di autenticazione nello stesso gateway VPN sono supportati solo con il tipo di tunnel OpenVPN.

### <a name="example-values"></a><a name="example"></a>Valori di esempio

È possibile usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

* **Nome VNet:** VNet1
* **Spazio di indirizzi:** 10.1.0.0/16<br>Per questo esempio, viene usato un solo spazio indirizzi. È possibile avere più di uno spazio indirizzi per la rete virtuale.
* **Nome della subnet:** FrontEnd
* **Intervallo di indirizzi subnet:** 10.1.0.0/24
* **Sottoscrizione:** se si hanno più sottoscrizioni, verificare di usare quella corretta.
* **Gruppo di risorse:** TestRG1
* **Località:** Stati Uniti orientali
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nome gateway di rete virtuale:** VNet1GW
* **Tipo di gateway:** VPN
* **Tipo di VPN:** basato su route
* **Nome indirizzo IP pubblico:** VNet1GWpip
* **Tipo di connessione:** Da punto a sito
* **Pool di indirizzi client:** 172.16.201.0/24<br>I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool di indirizzi client.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Creare una rete virtuale

Prima di iniziare, verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Gateway di rete virtuale

Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

>[!NOTE]
>Lo SKU del gateway Basic non supporta il tipo di tunnel OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Pool di indirizzi client

Il pool di indirizzi client è un intervallo di indirizzi IP privati specificati dall'utente. I client che si connettono dinamicamente tramite VPN da punto a sito ricevono un indirizzo IP da questo intervallo. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui viene effettuata la connessione o con la rete virtuale a cui ci si vuole connettere. Se si configurano più protocolli e SSTP è uno dei protocolli, il pool di indirizzi configurato viene suddiviso equamente tra i protocolli configurati.

1. Dopo avere creato il gateway di rete virtuale, andare alla sezione **Impostazioni** della pagina della rete virtuale. In **Impostazioni** selezionare **configurazione da punto a sito**. Selezionare **Configura ora** per aprire la pagina di configurazione.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Screenshot della pagina di configurazione da punto a sito." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Nella pagina **configurazione da punto a sito** è possibile configurare diverse impostazioni. Nella casella **pool di indirizzi** aggiungere l'intervallo di indirizzi IP privati che si vuole usare. I client VPN ricevono dinamicamente un indirizzo IP dall'intervallo specificato. Il subnet mask minimo è 29 bit per attivo/passivo e 28 bit per la configurazione attiva/attiva.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Screenshot del pool di indirizzi.":::

1. Passare alla sezione successiva per configurare l'autenticazione e i tipi di tunnel.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Tipi di autenticazione e tunnel

In questa sezione vengono configurati il tipo di autenticazione e il tipo di tunnel. Nella pagina **configurazione da punto a sito** , se non è visibile il **tipo di tunnel** o il **tipo di autenticazione**, il gateway usa lo SKU Basic. Lo SKU Basic non supporta l'autenticazione IKEv2 o RADIUS. Se si vogliono usare queste impostazioni, è necessario eliminare e ricreare il gateway usando uno SKU del gateway diverso.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Screenshot del tipo di autenticazione.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tipo di tunnel

Nella pagina **configurazione da punto a sito** selezionare **OpenVPN (SSL)** come tipo di tunnel.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Tipo di autenticazione

In **tipo di autenticazione** selezionare i tipi desiderati. Sono disponibili le opzioni seguenti:

* Certificato di Azure
* RADIUS
* Azure Active Directory

A seconda dei tipi di autenticazione selezionati, verranno visualizzati diversi campi delle impostazioni di configurazione che dovranno essere compilati. Immettere le informazioni necessarie e selezionare **Salva** nella parte superiore della pagina per salvare tutte le impostazioni di configurazione.

Per ulteriori informazioni sul tipo di autenticazione, vedere:

* [Certificato di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Pacchetto di configurazione del client VPN

I client VPN devono essere configurati con le impostazioni di configurazione client. Il pacchetto di configurazione del client VPN contiene file con le impostazioni per configurare i client VPN per connettersi a una VNet tramite una connessione P2S.

Per istruzioni su come generare e installare i file di configurazione del client VPN, usare l'articolo relativo alla configurazione:

* [Creare e installare i file di configurazione del client VPN per le configurazioni P2S di autenticazione del certificato di Azure nativo](point-to-site-vpn-client-configuration-azure-cert.md).
* [Autenticazione Azure Active Directory: configurare un client VPN per le connessioni del protocollo OpenVPN P2S](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Domande frequenti sulla connettività da punto a sito

Questa sezione contiene informazioni frequenti relative alle configurazioni da punto a sito. È anche possibile visualizzare le [domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md) per informazioni aggiuntive sul gateway VPN.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](../index.yml). Per altre informazioni sulla rete e sulle macchine virtuali, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/network-overview.md).

Per informazioni sulla risoluzione dei problemi della connessione da punto a sito, vedere [Risoluzione dei problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
