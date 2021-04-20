---
title: Protezione DDoS di Azure di continuità aziendale standard | Microsoft Docs
description: Informazioni sulle attività da eseguire in caso di interruzione di un servizio di Azure che influisce Protezione DDoS di Azure Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730452"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Protezione DDoS di Azure Standard : continuità aziendale

La continuità aziendale e il ripristino di emergenza Protezione DDoS di Azure Standard consentono all'azienda di continuare a operare in caso di interruzione. Questo articolo illustra la disponibilità (all'interno dell'area) e il ripristino di emergenza.

## <a name="overview"></a>Panoramica
Protezione DDoS di Azure Standard protegge gli indirizzi IP pubblici nelle reti virtuali. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede modifiche alle applicazioni o alle risorse.

Una rete virtuale è una rappresentazione logica della propria rete personalizzata nel cloud. Le reti virtuali fungono da limite di trust per ospitare le risorse, ad esempio gateway applicazione di Azure, Firewall di Azure e macchine virtuali di Azure. Viene creato nell'ambito di un'area. È possibile *creare* reti virtuali con lo stesso spazio di indirizzi in due aree diverse(ad esempio, Stati Uniti orientali e Stati Uniti occidentali), ma poiché hanno lo stesso spazio indirizzi, non è possibile connetterle tra loro. 

## <a name="business-continuity"></a>Continuità aziendale

Le applicazioni potrebbero essere soggette a interruzioni di varia natura. Un'area può essere interessata da un'interruzione totale dovuta a una calamità naturale o da un'emergenza parziale causata da un guasto di più dispositivi o servizi. L'impatto sulle reti virtuali protette è diverso in ognuna di queste situazioni.

**D: Cosa fare se si verifica un'interruzione per un'intera area? Ad esempio, se un'area è completamente tagliata a causa di una calamità naturale? Cosa accade alle reti virtuali ospitate nell'area?**

R: Durante il periodo di interruzione del servizio la rete virtuale e le risorse presenti nell'area interessata sono inaccessibili.

![Diagramma di rete virtuale semplice.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**D: In che modo è possibile ricreare la stessa rete virtuale in un'area diversa?**

R: Le reti virtuali sono risorse piuttosto semplici. Per creare una rete virtuale con lo stesso spazio di indirizzi in un'area diversa, è possibile richiamare le API di Azure. Per ricreare lo stesso ambiente presente nell'area interessata, si effettuano chiamate API per ridistribuire le risorse nelle reti virtuali presenti. Se si dispone di connettività locale, ad esempio in una distribuzione ibrida, è necessario distribuire un nuovo servizio Gateway VPN e connettersi alla rete locale.

Per creare una rete virtuale, vedere [Create a virtual network](../virtual-network/manage-virtual-network.md#create-a-virtual-network) (Creare una rete virtuale).

**D: È possibile ricreare in anticipo una replica di una rete virtuale di una determinata area in un'altra area?**

R: Sì, è possibile creare in anticipo due reti virtuali usando lo stesso spazio di indirizzi IP privati e le stesse risorse in due aree diverse. Se nella rete virtuale si ospitano servizi con connessione Internet, è possibile configurare Gestione traffico in modo da distribuire il traffico a livello geografico nell'area attiva. Tuttavia, non è possibile connettere due reti virtuali con lo stesso spazio degli indirizzi a una rete locale perché ciò comporterebbe problemi di routing. Al momento di un'emergenza e della perdita di una rete virtuale in un'area, è possibile connettere l'altra rete virtuale presente nell'area con il corrispondente spazio degli indirizzi alla rete locale.

Per creare una rete virtuale, vedere [Create a virtual network](../virtual-network/manage-virtual-network.md#create-a-virtual-network) (Creare una rete virtuale).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un piano di protezione DDoS.](manage-ddos-protection.md)