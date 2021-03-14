---
title: Versioni del software VMware
description: Versioni del software VMware supportate per la soluzione VMware di Azure.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462516"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Le versioni del software VMware usate nelle nuove distribuzioni dei cluster di cloud privati della soluzione VMware di Azure sono:

| Software              |    Versione   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX-T <br />**Nota:** NSX-T è l'unica versione supportata di NSX.               |      2.5     |


Nuovi cluster aggiunti a un cloud privato esistente, viene applicata la versione del software attualmente in esecuzione. Per ulteriori informazioni, vedere la pagina relativa ai [requisiti della versione del software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Gli aggiornamenti del pacchetto software del cloud privato conservano il software entro una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono differire dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN). È possibile trovare i criteri e i processi di aggiornamento generali per il software della piattaforma della soluzione VMware di Azure descritta in [aggiornamenti e aggiornamenti del cloud privato](../concepts-upgrades.md).

