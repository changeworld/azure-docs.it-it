---
title: Versioni del software VMware
description: Versioni del software VMware supportate per la soluzione VMware di Azure.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825074"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Le versioni software correnti del software VMware usato nei cluster di cloud privato della soluzione VMware di Azure sono:

| Software              |    Versione   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>L'unica versione supportata di NSX è NSX-T.

Per qualsiasi nuovo cluster in un cloud privato, la versione del software corrisponde a quella attualmente in esecuzione. Per ogni nuovo cloud privato in una sottoscrizione, viene installata la versione più recente dello stack di software. Per ulteriori informazioni, vedere la pagina relativa ai [requisiti della versione del software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Gli aggiornamenti del pacchetto software del cloud privato conservano il software entro una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono differire dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN). È possibile trovare i criteri e i processi di aggiornamento generali per il software della piattaforma della soluzione VMware di Azure descritta in [aggiornamenti e aggiornamenti del cloud privato](../concepts-upgrades.md).

