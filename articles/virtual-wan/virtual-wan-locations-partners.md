---
title: Partner e località WAN virtuali di Azure | Microsoft Docs
description: Questo articolo contiene un elenco di partner WAN virtuali di Azure e località dell'hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: cherylmc
ms.custom: references_regions
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 4a02a02eeb40c1a61df88c49bf967b2ca57befc3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489328"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Posizioni dei partner e degli hub virtuali della rete WAN virtuale di Azure

Questo articolo fornisce informazioni sulle aree e i partner supportati da WAN virtuali per la connettività all'hub virtuale.

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. La connessione e la configurazione possono essere eseguite manualmente oppure usando i dispositivi del provider tramite un partner WAN virtuale. L'uso di dispositivi partner consente di semplificare l'uso, la semplificazione della connettività e la gestione della configurazione.

La connettività dal dispositivo locale viene stabilita in modo automatico per l'hub virtuale. Un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). È possibile avere solo un hub per area.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Automazione della connettività IPsec del ramo dai partner

I dispositivi che si connettono alla rete WAN virtuale di Azure dispongono di un'automazione incorporata per la connessione. Questa è in genere impostata nell'interfaccia utente di gestione del dispositivo (o equivalente), che consente di impostare la gestione della configurazione e della connettività tra il dispositivo VPN della succursale e un endpoint VPN dell'hub virtuale di Azure (gateway VPN).

L'automazione di alto livello seguente è impostata nella console del dispositivo/centro di gestione:

* Autorizzazioni appropriate per il dispositivo per accedere al gruppo di risorse della rete WAN virtuale di Azure
* Caricamento del dispositivo della succursale nella rete WAN virtuale di Azure
* Download automatico di informazioni relative alla connettività di Azure
* Configurazione del dispositivo della succursale locale 

Alcuni partner di connettività possono estendere l'automazione in modo da includere la creazione della rete virtuale dell'hub virtuale di Azure e di un Gateway VPN. Per altre informazioni sull'automazione, vedere [linee guida di automazione per i partner WAN virtuali](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Partner di connettività IPsec del ramo

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

I partner seguenti sono disponibili sulla roadmap in base a un foglio di termini firmato tra le società che indica l'ambito di lavoro per automatizzare la connettività IPsec tra il dispositivo partner e i gateway VPN WAN virtuali di Azure: tecnologie 128, Arista, F5 reti, Oracle SD-WAN (Tino) e SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partner con offerte di hub virtuale integrate

Oltre a avere la connettività IPsec automatizzata per le succursali, alcuni partner offrono **appliance virtuali di rete (appliance virtuali)** che possono essere integrate direttamente nell'hub WAN virtuale di Azure.  Ciò consente ai clienti di terminare le connessioni di Branch in un'appliance di terze parti compatibile nell'hub virtuale.  

I partner che offrono l'appliance virtuale di rete nell'hub WAN virtuale devono:

* L'automazione della connettività IPsec è stata implementata dal dispositivo Branch e l'offerta dell'appliance virtuale di rete è stata caricata nell'hub WAN virtuale di Azure.
* Disporre di un'offerta di appliance virtuale di rete esistente disponibile in Azure Marketplace.

Se si è un partner e si hanno domande sull'appliance virtuale di gestione nell'offerta dell'hub virtuale, contattaci inviando un messaggio di posta elettronica a vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Partner appliance virtuale di hub integrato

Questi partner dispongono di offerte di **applicazioni gestite** ora disponibili per la distribuzione nell'hub WAN virtuale.

|Partner|Guida alla configurazione/procedura/distribuzione|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Guida alla distribuzione di Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Durante l'anteprima pubblica della rete WAN Cisco Services (CSR) nell'hub VWAN, Cisco richiede che il cliente finale venga registrato come cliente Cisco EFT (Early Field Trial) inviando un messaggio di posta elettronica a vwan_public_preview@external.cisco.com e richiedendo la guida alla distribuzione di vManage. 
|[SD-WAN VMware nell'hub WAN virtuale](https://sdwan.vmware.com/partners/microsoft) | Durante l'anteprima pubblica di VMware SD-WAN nell'hub VWAN, VMware richiede che il cliente si registri inviando un messaggio di posta elettronica a vhubsupport@vmware.com . [VMware SD-WAN nella Guida alla distribuzione dell'hub WAN virtuale](https://kb.vmware.com/s/article/82746)|

I partner seguenti sono disponibili per l'inclusione di appliance virtuale di rete nelle offerte dell'hub virtuale a breve in futuro: aviatrice, Citrix e versa.

## <a name="locations"></a><a name="locations"></a>Località

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md).

* Per altre informazioni su come automatizzare la connettività alla rete WAN virtuale di Azure, vedere [linee guida di automazione per i partner WAN virtuali](virtual-wan-configure-automation-providers.md).
