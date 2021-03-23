---
title: Aggiornamenti della piattaforma per la soluzione VMware di Azure
description: Informazioni sugli aggiornamenti della piattaforma per la soluzione VMware di Azure.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: ce25df2cb221c032f6dd430f292522fe86e69ceb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771771"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aggiornamenti della piattaforma per la soluzione VMware di Azure

Gli aggiornamenti importanti della soluzione VMware di Azure verranno applicati a partire dal 2021 marzo. Si riceverà una notifica tramite integrità dei servizi di Azure che include la sequenza temporale della manutenzione. Per altri dettagli sui processi e le funzionalità di aggiornamento principali della soluzione VMware di Azure, vedere [aggiornamenti e aggiornamenti del cloud privato della soluzione VMware di Azure](concepts-upgrades.md).

## <a name="march-15-2021"></a>15 marzo 2021 

- Il servizio di soluzione VMware di Azure eseguirà il lavoro di manutenzione fino al 19 marzo 2021 per aggiornare vCenter Server nel cloud privato alla versione server vCenter 6,7 Update 3L.

- Durante questo periodo, VMware vCenter non sarà disponibile e non sarà possibile gestire le macchine virtuali (arresto, avvio, creazione, eliminazione). Anche la scalabilità del cloud privato (aggiunta/rimozione di server e cluster) non sarà disponibile. La disponibilità elevata VMware continuerà a funzionare per fornire protezione per le macchine virtuali esistenti. 
 
Per altre informazioni su questa versione di vCenter, vedere [server VMware vCenter 6,7 aggiornamento 3L note](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)sulla versione.

## <a name="march-4-2021"></a>4 marzo 2021

- Le soluzioni VMware di Azure applicano le patch fino al 15 marzo 2021 ad ESXi nei cloud privati esistenti a [VMware ESXi 6,7, Patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- Le soluzioni alternative documentate per lo stack vSphere, come per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), verranno applicate anche fino al 15 marzo 2021.

>[!NOTE]
>Questa operazione non è problematica e non dovrebbe influire sui carichi di lavoro o sui servizi VMware di Azure. Durante la manutenzione, diversi avvisi VMware, ad esempio la _perdita della connettività di rete in DVPorts_ e la _perdita di ridondanza uplink in DVPorts_, vengono visualizzati in vCenter e cancellati automaticamente durante l'avanzamento della manutenzione.

## <a name="post-update"></a>Post-aggiornamento
Al termine, vengono visualizzate le versioni più recenti dei componenti VMware. Se si riscontrano problemi o si verificano domande, contattare il team di supporto aprendo un ticket di supporto.





