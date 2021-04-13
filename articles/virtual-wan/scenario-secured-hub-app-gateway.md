---
title: Proteggere il traffico tra il gateway applicazione e i pool back-end
titleSuffix: Azure Virtual WAN
description: 'Scenari per il routing: traffico protetto che viaggia attraverso un gateway applicazione distribuito in una VNet spoke connessa a un hub WAN virtuale protetto.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315578"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Scenario: proteggere il traffico tra il gateway applicazione e i pool back-end

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, il traffico di un utente passa ad Azure tramite un gateway applicazione distribuito in una VNet spoke connessa a un hub WAN virtuale protetto (hub WAN virtuale con un firewall di Azure). L'obiettivo è quello di usare il firewall di Azure nell'hub virtuale protetto per esaminare il traffico tra il gateway applicazione e i pool back-end.

In questo scenario sono disponibili due modelli di progettazione specifici, a seconda che il gateway applicazione e i pool back-end si trovino nello stesso VNet o in reti virtuali diversi.

* **Scenario 1:** Il gateway applicazione e i pool back-end si trovano nella stessa rete virtuale con peering a un hub WAN virtuale (subnet separate).
* **Scenario 2:** Il gateway applicazione e i pool back-end si trovano in reti virtuali diverse con peering a un hub WAN virtuale.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Scenario 1: stesso VNet

In questo scenario, il gateway applicazione e i pool back-end si trovano nella stessa rete virtuale con peering a un hub WAN virtuale (subnet separate).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagramma per lo scenario 1." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Flusso di lavoro

Attualmente, le route annunciate dalla tabella di route WAN virtuale alle reti virtuali spoke vengono applicate all'intera rete virtuale e non alle subnet della VNet spoke. Di conseguenza, le route definite dall'utente sono necessarie per abilitare questo scenario. Per informazioni sulle route definite dall'utente (UDR), vedere [route personalizzate della rete virtuale](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. In gestione firewall di Azure, nella rete virtuale spoke contenente il gateway applicazione e i pool back-end, selezionare **Abilita traffico Internet sicuro** e **Abilita traffico privato protetto**.
1. Configurare le route definite dall'utente (UDR) nella subnet del gateway applicazione.

   * Per assicurarsi che il gateway applicazione sia in grado di inviare il traffico direttamente a Internet, specificare i seguenti UDR:

     * **Prefisso Indirizzo:** 0.0.0.0.0/0
     * **Hop successivo:** Internet

   * Per assicurarsi che il gateway applicazione sia in grado di inviare il traffico al pool back-end tramite il firewall di Azure nell'hub WAN virtuale, specificare le UDR seguenti:

      * **Prefisso Indirizzo:** Subnet pool back-end (10.2.0.0/24)
      * **Hop successivo:** IP privato del firewall di Azure

1. Configurare una route definita dall'utente (UDR) nella subnet del pool back-end.

   * **Prefisso Indirizzo:** Subnet del gateway applicazione
   * **Hop successivo:** IP privato del firewall di Azure

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Scenario 2: reti virtuali diversi

In questo scenario, il gateway applicazione e i pool back-end si trovano in reti virtuali diverse che eseguono il peering a un hub WAN virtuale.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagramma per lo scenario 2." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Flusso di lavoro

Attualmente, le route annunciate dalla tabella di route WAN virtuale alle reti virtuali spoke vengono applicate all'intera rete virtuale e non alle subnet della VNet spoke. Di conseguenza, le route definite dall'utente sono necessarie per abilitare questo scenario. Per informazioni sulle route definite dall'utente (UDR), vedere [route personalizzate della rete virtuale](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. In **gestione firewall di Azure** selezionare **Abilita traffico Internet protetto** e **abilitare il traffico privato sicuro** in entrambe le reti virtuali spoke.

1. Configurare le route definite dall'utente (UDR) nella subnet del gateway applicazione. Per assicurarsi che il gateway applicazione sia in grado di inviare il traffico direttamente a Internet, specificare i seguenti UDR:

   * **Prefisso Indirizzo:** 0.0.0.0.0/0
   * **Hop successivo:** Internet

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
* Per altre informazioni sulle route definite dall'utente, vedere [route personalizzate della rete virtuale](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* Per informazioni sugli hub virtuali protetti con WAN virtuale, vedere [Hub virtuali protetti](../firewall-manager/secured-virtual-hub.md).
