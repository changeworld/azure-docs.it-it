---
title: Che cos'è il server di route di Azure (anteprima)?
description: Informazioni sul modo in cui il server di routing di Azure può semplificare il routing tra l'appliance virtuale di rete e la rete virtuale.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 98527ea5227fcdc2c35dbe3579657368e30fcdaa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680647"
---
# <a name="what-is-azure-route-server-preview"></a>Che cos'è il server di route di Azure (anteprima)? 

Il server di route di Azure semplifica il routing dinamico tra l'appliance virtuale di rete e la rete virtuale. Consente di scambiare informazioni di routing direttamente tramite il protocollo di routing Border Gateway Protocol (BGP) tra qualsiasi appliance virtuale di rete che supporta il protocollo di routing BGP e la rete SDN (software defined Network) di Azure nella rete virtuale di Azure (VNET) senza la necessità di configurare o gestire manualmente le tabelle di route. Il server di route di Azure è un servizio completamente gestito ed è configurato con disponibilità elevata.

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Come funziona?

Il diagramma seguente illustra il funzionamento del server di route di Azure con un'appliance virtuale di rete SDWAN e un appliance di sicurezza di rete in una rete virtuale. Una volta stabilito il peering BGP, il server di route di Azure riceverà una route locale (10.250.0.0/16) dal dispositivo SDWAN e una route predefinita (0.0.0.0/0) dal firewall. Queste route vengono quindi configurate automaticamente nelle VM nella rete virtuale. Di conseguenza, tutto il traffico destinato alla rete locale verrà inviato al dispositivo SDWAN. Mentre tutto il traffico associato a Internet verrà inviato al firewall. Nella direzione opposta, il server di route di Azure invierà l'indirizzo di rete virtuale (10.1.0.0/16) a entrambi i appliance virtuali. Il dispositivo SDWAN può propagarlo ulteriormente alla rete locale.

![Diagramma che mostra il server di route di Azure configurato in una rete virtuale.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Vantaggi principali 

Il server di route di Azure semplifica la configurazione, la gestione e la distribuzione dell'appliance virtuale di rete nella rete virtuale.  

* Non è più necessario aggiornare manualmente la tabella di routing nell'appliance virtuale di rete ogni volta che vengono aggiornati gli indirizzi della rete virtuale. 

* Non è più necessario aggiornare manualmente le [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md) ogni volta che l'appliance virtuale di sistema annuncia nuove route o ritira quelle obsolete. 

* Non è più necessario configurare un servizio di bilanciamento del carico davanti all'appliance virtuale di sistema per motivi di resilienza o prestazioni. Quando si esegue il peering di più istanze dell'appliance virtuale di sistema con il server di route di Azure, è possibile configurare gli attributi BGP nell'appliance virtuale di Azure Questi attributi BGP consentono al server di routing di Azure quale istanza di appliance virtuale di Azure deve essere attiva o passiva. 

* L'interfaccia tra l'appliance virtuale di Azure e il server di route di Azure si basa su un protocollo standard comune. Fino a quando l'appliance virtuale di sistema supporta BGP, è possibile collegarlo al server di route di Azure. Per altre informazioni, vedere [protocolli di routing supportati da server di route](route-server-faq.md#protocol).

* È possibile distribuire il server di route di Azure in qualsiasi rete virtuale nuova o esistente. 

## <a name="faq"></a>Domande frequenti

Per domande frequenti sul server di route di Azure, vedere domande [frequenti sul server di route di Azure](route-server-faq.md).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come configurare il server di route di Azure.](quickstart-configure-route-server-portal.md)
- [Informazioni sul funzionamento del server di routing di Azure con Azure ExpressRoute e la VPN di Azure](expressroute-vpn-support.md)
