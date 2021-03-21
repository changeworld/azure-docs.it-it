---
title: 'Rete virtuale di Azure: concetti e procedure consigliate'
description: Informazioni sui concetti e sulle procedure consigliate per la rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223619"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Concetti e procedure consigliate per la rete virtuale di Azure

Questo articolo descrive i concetti chiave e le procedure consigliate per la rete virtuale di Azure (VNet).

## <a name="vnet-concepts"></a>Concetti di VNet

- **Spazio indirizzi**: quando si crea una rete virtuale, è necessario specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse di una rete virtuale un indirizzo IP privato dello spazio indirizzi specificato. Se, ad esempio, si distribuisce una macchina virtuale in una rete virtuale con spazio indirizzi 10.0.0.0/16, alla macchina virtuale verrà assegnato un indirizzo IP privato come 10.0.0.4.
- **Subnet:** le subnet consentono di segmentare la rete virtuale in una o più reti secondarie e di allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet. È quindi possibile distribuire le risorse di Azure in una subnet specifica. Proprio come le reti tradizionali, le subnet consentono di segmentare lo spazio indirizzi della rete virtuale in segmenti appropriati per la rete interna dell'organizzazione. In questo modo, anche l'allocazione degli indirizzi risulta più efficiente. È possibile proteggere le risorse all'interno delle subnet con i gruppi di sicurezza di rete. Per altre informazioni, vedere [Gruppi di sicurezza di rete](./network-security-groups-overview.md).
- **Aree di Azure**: l'ambito di una rete virtuale è una singola area/località; tuttavia, è possibile connettere più reti virtuali da diverse aree di Azure usando il peering di rete virtuale.
- **Sottoscrizione:** l'ambito di una rete virtuale è una sottoscrizione. È possibile implementare più reti virtuali in ogni [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [area](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) di Azure.

## <a name="best-practices"></a>Procedure consigliate

Durante la creazione della rete in Azure, è importante tenere presente i principi di progettazione universale seguenti:

- Verificare che gli spazi indirizzi non si sovrappongano. Assicurarsi che lo spazio indirizzi della rete virtuale (blocco CIDR) non si sovrapponga con altri intervalli di rete dell'organizzazione.
- È importante che le subnet non coprano l'intero spazio indirizzi della rete virtuale, in modo da poter riservare una parte dello spazio indirizzi per esigenze future.
- È preferibile avere poche reti virtuali di grandi dimensioni invece di tante reti virtuali di piccole dimensioni. In questo modo, si eviterà infatti un sovraccarico di gestione.
- Proteggere le reti virtuali assegnando gruppi di sicurezza di rete alle subnet sottostanti.

## <a name="next-steps"></a>Passaggi successivi

 Per iniziare a usare una rete virtuale, crearne una, distribuirvi alcune macchine virtuali e comunicare tra le macchine virtuali. Per informazioni sulla procedura, vedere la guida introduttiva [Creare una rete virtuale](quick-create-portal.md).