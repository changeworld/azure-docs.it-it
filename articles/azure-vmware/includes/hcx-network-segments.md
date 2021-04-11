---
title: Segmenti di rete di VMware HCX
description: Per VMware HCX sono necessarie quattro reti.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251347"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Esistono diversi modi per configurare i segmenti di rete VMware HCX in locale. Di seguito viene illustrata una semplice configurazione che supporta un caso di utilizzo pilota o di piccole dimensioni.  Quando si progetta per centinaia o migliaia di carichi di lavoro, potrebbe essere necessario modificare questa configurazione, a seconda delle esigenze della migrazione.  

Per preparare la distribuzione di VMware HCX per il supporto del caso di utilizzo pilota o di piccole dimensioni, identificare gli elementi seguenti:

- **Rete di gestione:** Quando si distribuisce VMware HCX in locale, è necessario identificare una rete di gestione per VMware HCX.  In genere, si tratta della stessa rete di gestione usata dal cluster VMware locale.  Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero essere necessari numeri maggiori, a seconda della scala della distribuzione oltre il caso di utilizzo pilota o di piccole dimensioni.

  > [!NOTE]
  > Preparazione per ambienti di grandi dimensioni, anziché usare la rete di gestione usata per il cluster VMware locale, creare una nuova rete/26 e presentare la rete come gruppo di porte al cluster VMware locale.  È quindi possibile creare fino a 10 mesh del servizio e 60 Extender di rete (-1 per mesh del servizio). È possibile estendere **otto** reti per Extender di rete usando cloud privati della soluzione VMware di Azure.
  >

- **Rete uplink:** Quando si distribuisce VMware HCX in locale, è necessario identificare una rete uplink per VMware HCX. Utilizzare la stessa rete che verrà utilizzata per la rete di gestione. 

- **rete VMotion:** Quando si distribuisce VMware HCX in locale, è necessario identificare una rete vMotion per VMware HCX.  In genere, si tratta della stessa rete usata per vMotion dal cluster VMware locale.  Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero essere necessari numeri maggiori, a seconda della scala della distribuzione oltre il caso di utilizzo pilota o di piccole dimensioni.

   La rete vMotion deve essere esporta in un commutatore virtuale distribuito o vSwitch0. In caso contrario, modificare l'ambiente per adattarlo.

   > [!NOTE]
   > Molti ambienti VMware utilizzano segmenti di rete non indirizzati per vMotion, che non costituisce alcun problema.
  
- **Rete di replica:** Quando si distribuisce VMware HCX in locale, sarà necessario definire una rete di replica. Usare la stessa rete usata per le reti di gestione e uplink.  Se gli host del cluster locale usano una rete VMkernel di replica dedicata, riservare **due** indirizzi IP in questo segmento di rete e usare la rete VMkernel di replica per la rete di replica.
