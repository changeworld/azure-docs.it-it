---
title: Versioni del software VMware
description: Versioni del software VMware supportate per la soluzione VMware di Azure.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106098122"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Le versioni del software VMware usate nelle nuove distribuzioni dei cluster di cloud privati della soluzione VMware di Azure sono:

| Software              |    Versione   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3l    | 
| ESXi                  |    6,7 U3l    | 
| vSAN                  |    6,7 U3l    |
| NSX-T <br />**Nota:** NSX-T è l'unica versione supportata di NSX.               |      3.1.1     |


Nuovi cluster aggiunti a un cloud privato esistente, viene applicata la versione del software attualmente in esecuzione. Per ulteriori informazioni, vedere la pagina relativa ai [requisiti della versione del software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Gli aggiornamenti del pacchetto software del cloud privato conservano il software entro una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono differire dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN). È possibile trovare i criteri e i processi di aggiornamento generali per il software della piattaforma della soluzione VMware di Azure descritta in [aggiornamenti e aggiornamenti del cloud privato](../concepts-upgrades.md).

