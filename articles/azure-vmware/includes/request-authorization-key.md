---
title: Richiedi chiave di autorizzazione per ExpressRoute
description: Procedura per richiedere una chiave di autorizzazione per ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026974"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Nella finestra di portale di Azure, nella sezione **connettività** , nella scheda **ExpressRoute** Selezionare **+ Richiedi una chiave di autorizzazione**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Screenshot che illustra come richiedere una chiave di autorizzazione ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Specificare un nome e selezionare **Crea**. 
      
   La creazione della chiave potrebbe richiedere circa 30 secondi. Una volta creata, la nuova chiave viene visualizzata nell'elenco delle chiavi di autorizzazione per il cloud privato.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Screenshot che mostra la chiave di autorizzazione Copertura globale di ExpressRoute.":::
  
1. Copiare la chiave di autorizzazione e l'ID ExpressRoute. Verranno usati per completare il peering.  

   > [!NOTE]
   > La chiave di autorizzazione scompare dopo un certo periodo di tempo, quindi copiarla non appena viene visualizzata.