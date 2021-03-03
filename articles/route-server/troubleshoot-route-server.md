---
title: Risolvere i problemi del server di route di Azure
description: Informazioni su come risolvere i problemi del server di route di Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679863"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Risoluzione dei problemi del server di route di Azure

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bgp-connectivity-issues"></a>Problemi di connettività BGP

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Perché è in corso il peering BGP tra l'appliance virtuale di Azure e il server di route di Azure ("sbattimento")?

La causa del problema potrebbe essere dovuta all'impostazione del timer BGP. Per impostazione predefinita, il timer keep-alive sul server di route di Azure è impostato su 60 secondi e il timer di attesa è 180 secondi.

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>Perché è possibile eseguire il ping dall'appliance virtuale di Azure all'indirizzo IP del peer BGP nel server di route di Azure, ma dopo aver configurato il peering BGP tra di essi, non è più possibile eseguire il ping dello stesso IP? Perché il peering BGP diventa inattivo?

In alcune appliance virtuale di Azure è necessario aggiungere una route statica per la subnet del server di route di Azure. Ad esempio, se il server di route di Azure si trova in 10.0.255.0/27 e l'appliance virtuale di sistema è in 10.0.1.0/24, è necessario aggiungere la route seguente alla tabella di routing nell'appliance virtuale di Azure:

| Route | Hop successivo |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 è l'indirizzo IP del gateway predefinito nella subnet in cui è ospitata l'appliance virtuale di rete (o più precisamente una delle schede di rete).

## <a name="bgp-route-issues"></a>Problemi di route BGP

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Perché l'appliance virtuale di Azure non riceve route dal server di route di Azure anche se il peering BGP è attivo?

Il numero ASN utilizzato dal server di route di Azure è 65515. Assicurarsi di configurare un ASN diverso per l'appliance virtuale di sistema in modo che sia possibile stabilire una sessione "eBGP" tra l'appliance virtuale di Azure e il server di route di Azure in modo che la propagazione delle route possa verificarsi automaticamente

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>Il peering BGP tra l'appliance virtuale di Azure e il server di route di Azure è attivo. È possibile visualizzare le route scambiate correttamente tra di esse. Perché le route dell'appliance virtuale di servizio non sono presenti nella tabella di routing effettiva della macchina virtuale? 

* Se la macchina virtuale si trova nella stessa VNet dell'appliance virtuale di sistema e del server di route di Azure:

     Il server di route di Azure espone due indirizzi IP del peer BGP, ospitati in due macchine virtuali che condividono la responsabilità di inviare le route a tutte le altre macchine virtuali in esecuzione nella rete virtuale. Ogni appliance virtuale di rete deve configurare due sessioni BGP identiche (ad esempio, usare lo stesso numero di percorso e annunciare lo stesso set di route) per le due macchine virtuali in modo che le macchine virtuali nella rete virtuale possano ottenere informazioni di routing coerenti dal server di route di Azure. Vedere il diagramma riportato di seguito.

    ![Diagramma che mostra un'appliance virtuale di rete con il server di route.](./media/faq/network-virtual-appliances.png)

    Se si dispone di due o più istanze dell'appliance virtuale di dispositivo, è *possibile* pubblicizzare percorsi diversi per la stessa route da diverse istanze di appliance virtuale di dispositivo, se si vuole designare un'istanza di appliance virtuale di appliance come attiva e l'altra passiva.

* Se la VM si trova in una rete virtuale diversa da quella che ospita l'appliance virtuale di rete e il server di route di Azure. Controllare se il peering VNet è abilitato tra i due reti virtuali *e* se usa il server di route remoto è abilitato nel VNet della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare un server di route di Azure](quickstart-configure-route-server-powershell.md)
