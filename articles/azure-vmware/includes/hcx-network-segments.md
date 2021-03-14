---
title: Segmenti di rete di VMware HCX
description: Per VMware HCX sono necessarie quattro reti.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462301"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Per VMware HCX sono necessarie quattro reti:

- **Rete di gestione:** In genere, si tratta della stessa rete di gestione usata dal cluster di soluzioni VMware di Azure. Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero esserne necessari di più a seconda della distribuzione.

   > [!NOTE]
   > Il metodo consigliato consiste nel creare una rete/26 perché è possibile usare fino a 10 mesh di servizio e 60 Extender di rete (-1 per mesh di servizi). È possibile estendere **otto** reti per Extender di rete usando cloud privati della soluzione VMware di Azure.
   >
   
- **rete VMotion:** In genere, si tratta della stessa rete usata per vMotion nel cluster della soluzione VMware di Azure.  Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero esserne necessari di più a seconda della distribuzione.  

   La rete vMotion deve essere esporta in un commutatore virtuale distribuito o vSwitch0. In caso contrario, modificare l'ambiente.

   > [!NOTE]
   > Questa rete può essere privata (non indirizzata).

- **Rete uplink:** si vuole creare una nuova rete per VMware HCX Uplink ed estenderla nel cluster vSphere tramite un gruppo di porte. Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero esserne necessari di più a seconda della distribuzione.  

   > [!NOTE]
   > Il metodo consigliato consiste nel creare una rete/26 perché è possibile usare fino a 10 mesh di servizio e 60 Extender di rete (-1 per mesh di servizi). È possibile estendere **otto** reti per Extender di rete usando cloud privati della soluzione VMware di Azure.
   >
   
- **Rete di replica:** Operazione facoltativa. si vuole creare una nuova rete per VMware HCX Replication ed estendere tale rete nel cluster vSphere tramite un gruppo di porte. Come minimo, identificare **due** indirizzi IP in questo segmento di rete per VMware HCx. Potrebbero esserne necessari di più a seconda della distribuzione.

   > [!NOTE]
   > Questa configurazione è possibile solo quando gli host del cluster locale usano una rete VMkernel di replica dedicata.  Se per il cluster locale non è definita una rete VMkernel di replica dedicata, non è necessario crearla.
