---
title: Segmenti di rete di VMware HCX
description: Per VMware HCX sono necessarie quattro reti.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999630"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Per VMware HCX sono necessarie quattro reti:

- **Rete di gestione:** si tratta in genere della stessa rete di gestione usata nel cluster vSphere. Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.

   > [!NOTE]
   > Il metodo consigliato consiste nel creare una rete /26. In una rete /26 è possibile usare fino a 10 mesh di servizi e 60 controlli Extender di rete (-1 per mesh di servizi). È possibile adattare otto reti per controllo Extender di rete usando cloud privati della soluzione Azure VMware.
   >
   
- **Rete vMotion:** si tratta in genere della stessa rete usata per vMotion nel cluster vSphere.  Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.  

   La rete vMotion deve essere esporta in un commutatore virtuale distribuito o vSwitch0. In caso contrario, modificare l'ambiente.

   > [!NOTE]
   > Questa rete può essere privata (non indirizzata).

- **Rete uplink:** si vuole creare una nuova rete per VMware HCX Uplink ed estenderla nel cluster vSphere tramite un gruppo di porte. Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.  

   > [!NOTE]
   > Il metodo consigliato consiste nel creare una rete /26. In una rete /26 è possibile usare fino a 10 mesh di servizi e 60 controlli Extender di rete (-1 per mesh di servizi). È possibile adattare otto reti per controllo Extender di rete usando cloud privati della soluzione Azure VMware.
   >
   
- **Rete di replica:** Operazione facoltativa. si vuole creare una nuova rete per VMware HCX Replication ed estendere tale rete nel cluster vSphere tramite un gruppo di porte. Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.

   > [!NOTE]
   > Questa configurazione è possibile solo quando gli host del cluster locale usano una rete VMkernel di replica dedicata.  Se per il cluster locale non è definita una rete VMkernel di replica dedicata, non è necessario crearla.
