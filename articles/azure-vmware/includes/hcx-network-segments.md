---
title: Segmenti di rete di VMware HCX
description: Per VMware HCX sono necessarie quattro reti.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103621944"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Esistono diversi modi per configurare i segmenti di rete VMware HCX in locale. Di seguito viene illustrata una semplice configurazione che supporta un caso di utilizzo pilota o di piccole dimensioni.  Quando si progetta per centinaia o migliaia di carichi di lavoro, potrebbe essere necessario modificare questa configurazione, a seconda delle esigenze della migrazione.  

Per preparare la distribuzione di VMware HCX per il supporto del caso di utilizzo pilota o di piccole dimensioni, identificare gli elementi seguenti:

- **Rete di gestione:** Quando si distribuisce VMware HCX in locale, è necessario definire una rete di gestione.  In genere, si tratta della stessa rete di gestione usata dal cluster VMware locale.  Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero essere necessari numeri maggiori, a seconda della scala della distribuzione oltre il caso di utilizzo pilota o di piccole dimensioni.

> [!NOTE]
   > Il metodo consigliato consiste nel creare una rete/26 perché è possibile usare fino a 10 mesh di servizio e 60 Extender di rete (-1 per mesh di servizi). È possibile estendere **otto** reti per Extender di rete usando cloud privati della soluzione VMware di Azure.
   >
   
- **rete VMotion:** Quando si distribuisce VMware HCX in locale, è necessario definire una rete vMotion.  In genere, si tratta della stessa rete usata per vMotion dal cluster VMware locale.  Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero essere necessari numeri maggiori, a seconda della scala della distribuzione oltre il caso di utilizzo pilota o di piccole dimensioni.

   La rete vMotion deve essere esporta in un commutatore virtuale distribuito o vSwitch0. In caso contrario, modificare l'ambiente per adattarlo.

   > [!NOTE]
   > Molti ambienti VMware utilizzano segmenti di rete non indirizzati per vMotion, che non costituisce alcun problema.

- **Rete uplink:** Quando si distribuisce VMware HCX in locale, è necessario definire una rete uplink. Usare la rete di gestione definita come rete uplink.
   
- **Rete di replica:** Quando si distribuisce VMware HCX in locale, sarà necessario definire una rete di replica. Utilizzare la rete di gestione definita come rete di replica.  Se gli host del cluster locale usano una rete VMkernel di replica dedicata, riservare **due** indirizzi IP in questo segmento di rete e usare la rete VMkernel di replica per la rete di replica.
