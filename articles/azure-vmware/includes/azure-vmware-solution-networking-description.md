---
title: Rete e connettività della soluzione Azure VMware
description: Descrizione delle funzionalità di rete e connettività della soluzione Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462584"
---
<!-- Used in introduction.md and concepts-networking.md -->

La soluzione VMware di Azure offre un ambiente cloud privato accessibile da risorse locali e basate su Azure. I servizi come Azure ExpressRoute, le connessioni VPN o la rete WAN virtuale di Azure forniscono la connettività. Per abilitare questi servizi sono necessari specifici intervalli di indirizzi di rete e porte del firewall.

Quando si distribuisce un cloud privato, vengono create reti private per la gestione, il provisioning e vMotion. Usare queste reti private per accedere a vCenter e NSX-T Manager e per la distribuzione o la migrazione tramite vMotion delle macchine virtuali.  

Per connettere i cloud privati agli ambienti locali viene usato il servizio Copertura globale di ExpressRoute. Per la connessione è necessaria una rete virtuale con un circuito ExpressRoute locale nella sottoscrizione.

Le macchine virtuali distribuite nel cloud privato sono accessibili a Internet tramite la funzionalità IP pubblico WAN virtuale di Azure.  Per impostazione predefinita, l'accesso a Internet è disabilitato per i nuovi cloud privati. Per altre informazioni, vedere [Come usare la funzionalità degli indirizzi IP pubblici nella soluzione Azure VMware](../public-ip-usage.md).

