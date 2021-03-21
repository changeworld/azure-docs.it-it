---
title: Richiedi chiave di autorizzazione per ExpressRoute
description: Procedura per richiedere una chiave di autorizzazione per ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491843"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Nella finestra di portale di Azure, nella sezione **connettività** , nella scheda **ExpressRoute** Selezionare **+ Richiedi una chiave di autorizzazione**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Screenshot che illustra come richiedere una chiave di autorizzazione ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Specificare un nome e selezionare **Crea**. 
      
   La creazione della chiave potrebbe richiedere circa 30 secondi. Una volta creata, la nuova chiave viene visualizzata nell'elenco delle chiavi di autorizzazione per il cloud privato.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Screenshot che mostra la chiave di autorizzazione Copertura globale di ExpressRoute.":::
  
1. Prendere nota della chiave di autorizzazione e dell'ID ExpressRoute. Verranno usati per completare il peering.  

   > [!NOTE]
   > La chiave di autorizzazione scompare dopo un certo periodo di tempo, quindi copiarla non appena viene visualizzata.