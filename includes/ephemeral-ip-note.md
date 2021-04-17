---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529558"
---
> [!NOTE]
> Azure offre un indirizzo IP ffimero per le macchine virtuali di Azure a cui non è assegnato un indirizzo IP pubblico o che sono nel pool back-end di un indirizzo Azure Load Balancer. Il meccanismo IP ffemero fornisce un indirizzo IP in uscita non configurabile. 
>
>L'indirizzo IP ffemero è disabilitato quando un indirizzo IP pubblico viene assegnato alla macchina virtuale o la macchina virtuale viene inserita nel pool back-end di un Load Balancer Standard con o senza regole in uscita. Se una [NAT di rete virtuale di Azure](../articles/virtual-network/nat-overview.md) gateway virtuale viene assegnata alla subnet della macchina virtuale, l'indirizzo IP ffemero è disabilitato.
>
> Per altre informazioni sulle connessioni in uscita in Azure, vedere [Uso di SNAT (Source Network Address Translation) per le connessioni in uscita.](../articles/load-balancer/load-balancer-outbound-connections.md)