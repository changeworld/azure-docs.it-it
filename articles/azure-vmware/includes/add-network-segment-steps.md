---
title: Aggiungere un segmento di rete NSX-T
description: Procedura per aggiungere un segmento di rete NSX-T per la soluzione Azure VMware.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 7db45650588d37c39e7d156fa189b3ff7da2239f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "100515074"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. In NSX-T Manager selezionare **Rete** > **Segmenti** e quindi selezionare **Aggiungi segmento**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Screenshot che mostra come aggiungere un nuovo segmento":::

1. Selezionare **Aggiungi segmento** e immettere un nome per il segmento.

1. Selezionare il gateway di primo livello (TNTxx-T1) come **gateway connesso** e lasciare il **tipo** flessibile.

1. Selezionare il valore preconfigurato di **Transport Zone** (Zona di trasporto) per l'overlay (TNTxx-OVERLAY-TZ) e quindi selezionare **Set Subnets** (Imposta subnet). 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Impostare Nome del segmento, Gateway connesso, Tipo e Zona di trasporto, quindi selezionare&quot;Imposta subnet.":::

1. Immettere l'indirizzo IP del gateway e quindi selezionare **Add** (Aggiungi). 

   >[!IMPORTANT]
   >L'indirizzo IP deve essere incluso in un blocco di indirizzi RFC1918 non sovrapposto, che consente di connettersi alle macchine virtuali nel nuovo segmento.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Impostare l'indirizzo IP del gateway per il nuovo segmento e quindi selezionare ADD (AGGIUNGI).":::

1. Selezionare **Apply** (Applica) e quindi **Save** (Salva).

1. Selezionare **No** per rifiutare l'opzione per continuare a configurare il segmento. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Rifiutare di configurare ulteriormente il segmento di rete appena creato selezionando NO.":::

1. Verificare la presenza del nuovo segmento di rete. In questo esempio il nuovo segmento di rete è **ls01**.

   1. In NSX-T Manager selezionare **Rete** > **Segmenti**. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Verificare che il nuovo segmento di rete sia presente in NSX-T.":::

   1. In vCenter selezionare **Networking > SDDC-Datacenter** (Rete > SDDC-Datacenter).

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Verificare che il nuovo segmento di rete sia presente in vCenter.":::